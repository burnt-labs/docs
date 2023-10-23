# Migration

Migration involves the process of updating or replacing the code of an existing smart contract, and sometimes includes modifying state data.

CosmWasm simplifies the contract migration process. When a contract is instantiated, there is an optional admin field that can be configured. If left unspecified, the contract remains immutable. However, if an external account or governance contract is assigned as the admin, that entity gains the authority to initiate migration. The admin can also transfer ownership to another account or make the contract entirely immutable by setting the admin field to an empty value.

This is where the CW2 specification plays a vital role. It defines a special Singleton that holds the contract's name and version information, which must be stored by all CW2-compliant contracts during instantiation. When the migration function is triggered, the newly created contract can reference this information to determine if it's compatible with migrating from the previous contract.

The CW2 Specification introduces a set\_contract\_version function that should be used when instantiating a new contract through the instantiate function. For contracts undergoing migration, it's crucial to utilize the set\_contract\_version function as part of the migration logic within the migrate(...) function, rather than in the instantiate function, to update the contract's versioning during the migration process.

```
[codebox]
const CONTRACT_NAME: &str = env!("CARGO_PKG_NAME");
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(deps: DepsMut, env: Env, info: MessageInfo, msg: InstantiateMsg) -> Response {
    // Use CW2 to set the contract version, this is needed for migrations
    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;
}

```

In the case of a migrating contract, aside from employing the set\_contract\_version function, you can also make use of the get\_contract\_version function to ascertain the previous version of the contract. Ensuring, for instance, that the update occurs only if the version being upgraded is more recent than the original contract version is of paramount importance.

```
[codebox]
use semver::Version;
// Migrate contract if version is lower than current version
#[entry_point]
pub fn migrate(deps: DepsMut, _env: Env, _msg: Empty) -> Result<Response, ContractError> {
    let version: Version = CONTRACT_VERSION.parse()?;
    let storage_version: Version = get_contract_version(deps.storage)?.version.parse()?;
    if storage_version < version {
        set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;
        // If state structure changed in any contract version in the way migration is needed, it
        // should occur here
    }
    Ok(Response::new())
}

```

Both the set\_contract\_version and get\_contract\_version methods function with an Item data structure provided by cw\_storage\_plus, which is responsible for managing this information.

```
[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct ContractVersion {
    /// contract is a globally unique identifier for the contract.
    /// it should build off standard namespacing for whichever language it is in,
    /// and prefix it with the registry we use.
    /// For rust we prefix with `crates.io:`, to give us eg. `crates.io:cw20-base`
    pub contract: String,
    /// version is any string that this implementation knows. It may be simple counter "1", "2".
    /// or semantic version on release tags "v0.7.0", or some custom feature flag list.
    /// the only code that needs to understand the version parsing is code that knows how to
    /// migrate from the given contract (and is tied to it's implementation somehow)
    pub version: String,
}

```

Therefore, an example in serialized form may look like this:

```
{
    "contract": "crates.io:cw20-base",
    "version": "v0.1.0"
}
```

## Setting up a contract for Migrations

Executing a contract migration involves three sequential steps. Firstly, create an updated version of the contract you intend to modify. Next, deploy the new code onto the blockchain, but refrain from instantiating it. Finally, employ a dedicated MsgMigrateContract transaction to direct the old contract toward the new code, thus completing the migration process.&#x20;

It's essential to note that during the migration process, the migrate function defined in the new contract is the one that gets executed, not the migrate function from the old code. Therefore, it is imperative for the new contract code to feature a properly defined migrate function that is appropriately exported as an entry point.

```
use cosmwasm_std::{
    Empty, Env, DepsMut, Response,
};
use crate::error::ContractError;
use cosmwasm_std::entry_point;
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn migrate(deps: DepsMut, _env: Env, _msg: Empty) -> Result<Response, ContractError> {
}

```

The migrate function offers the capability to make specific and granular changes to the State, akin to a database migration.

In the event that the migrate function returns an error, the transaction will terminate, all State alterations will be rolled back, and the migration will not proceed.

Listed below are various migration scenarios to provide insight into common use cases.

### Basic Contract Migration

This type of migration is one of the most commonly encountered scenarios. It involves a straightforward replacement of a contract's code. It's important to note that there are no safety checks in place, as there is no verification of a cw2::set\_contract\_version.

```
use cosmwasm_std::{ Env, DepsMut, Response };
use crate::error::ContractError;
use crate::msg::{ MigrateMsg };
use cosmwasm_std::entry_point;
const CONTRACT_NAME: &str = env!("CARGO_PKG_NAME");
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
#[entry_point]
pub fn migrate(deps: DepsMut, _env: Env, msg: MigrateMsg) -> Result<Response, ContractError> {
    // No state migrations performed, just returned a Response
    Ok(Response::default())
}

```

### Migration Based on Code Version and Contract Name

This is a more extensive migration example. The migrate function guarantees the following:

1. Migration from a contract of the same type by verifying its name.
2. Upgrading from an older version of the contract by checking its version.

```
const CONTRACT_NAME: &str = env!("CARGO_PKG_NAME");
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
#[entry_point]
pub fn migrate(deps: DepsMut, _env: Env, msg: MigrateMsg) -> Result<Response, ContractError> {
    let ver = cw2::get_contract_version(deps.storage)?;
    // ensure we are migrating from a compatible contract
    if ver.contract != CONTRACT_NAME {
        return Err(StdError::generic_err("Can only upgrade from same contract type").into());
    }
    // note: it's better to do a proper semver comparison, but a string comparison *usually* works
    #[allow(clippy::cmp_owned)]
    if ver.version >= CONTRACT_VERSION {
        return Err(StdError::generic_err("Cannot upgrade from a newer contract version").into());
    }
    // set the new version
    cw2::set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;
    // do any required state migrations...
    Ok(Response::default())

```

### Migrating with Semver Comparison

In this type of migration, Semver (Semantic Versioning) is used for comparison instead of performing a simple string comparison.

```
const CONTRACT_NAME: &str = env!("CARGO_PKG_NAME");
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
#[entry_point]
pub fn migrate(deps: DepsMut, _env: Env, msg: MigrateMsg) -> Result<Response, ContractError> {
    let version: Version = CONTRACT_VERSION.parse()?;
    let storage_version: Version = get_contract_version(deps.storage)?.version.parse()?;
    if storage_version < version {
        set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION)?;
        // If state structure changed in any contract version in the way migration is needed, it
        // should occur here
    }
    Ok(Response::default())
}

```

In this example, Semver is employed to facilitate version checks. To implement this, you will also need to include the Semver dependency in your cargo dependencies.

```
[dependencies]
semver = "1"

You can also implement Semver custom errors:

#[derive(Error, Debug, PartialEq)]
pub enum ContractError {
    #[error("Semver parsing error: {0}")]
    SemVer(String),
}
impl From<semver::Error> for ContractError {
    fn from(err: semver::Error) -> Self {
        Self::SemVer(err.to_string())
    }
}

```

### Updating Otherwise Immutable State with Migrate

This example showcases how a migration can be utilized to modify a value that would ordinarily be considered immutable. This functionality enables the alteration of the value exclusively during a migration when required.

```
#[entry_point]
pub fn migrate(deps: DepsMut, _env: Env, msg: MigrateMsg) -> Result<Response, HackError> {
    let data = deps
        .storage
        .get(CONFIG_KEY)
        .ok_or_else(|| StdError::not_found("State"))?;
    let mut config: State = from_slice(&data)?;
    config.verifier = deps.api.addr_validate(&msg.verifier)?;
    deps.storage.set(CONFIG_KEY, &to_vec(&config)?);
    Ok(Response::default())
}

```

In the example above, our MigrateMsg includes a verifier field that holds the new value for our contract's verifier field within the State. Assuming your contract doesn't expose an UpdateState or similar ExecuteMsgs, this method serves as the sole means to modify the verifier value.

### Leveraging Migrations to 'Burn' a Contract

Migrations can serve the purpose of abandoning an old contract and erasing its state. This technique has multiple use cases, and if necessary, you can refer to an example here:

```
#[entry_point]
pub fn migrate(deps: DepsMut, env: Env, msg: MigrateMsg) -> StdResult<Response> {
    // delete all state
    let keys: Vec<_> = deps
        .storage
        .range(None, None, Order::Ascending)
        .map(|(k, _)| k)
        .collect();
    let count = keys.len();
    for k in keys {
        deps.storage.remove(&k);
    }
    // get balance and send all to recipient
    let balance = deps.querier.query_all_balances(env.contract.address)?;
    let send = BankMsg::Send {
        to_address: msg.payout.clone(),
        amount: balance,
    };
    let data_msg = format!("burnt {} keys", count).into_bytes();
    Ok(Response::new()
        .add_message(send)
        .add_attribute("action", "burn")
        .add_attribute("payout", msg.payout)
        .set_data(data_msg))
}
#[entry_point]
pub fn migrate(deps: DepsMut, env: Env, msg: MigrateMsg) -> StdResult<Response> {
    // delete all state
    let keys: Vec<_> = deps
        .storage
        .range(None, None, Order::Ascending)
        .map(|(k, _)| k)
        .collect();
    let count = keys.len();
    for k in keys {
        deps.storage.remove(&k);
    }
    // get balance and send all to recipient
    let balance = deps.querier.query_all_balances(env.contract.address)?;
    let send = BankMsg::Send {
        to_address: msg.payout.clone(),
        amount: balance,
    };
    let data_msg = format!("burnt {} keys", count).into_bytes();
    Ok(Response::new()
        .add_message(send)
        .add_attribute("action", "burn")
        .add_attribute("payout", msg.payout)
        .set_data(data_msg))
}

```

In the example above, the migration process involves the complete deletion of the contract's state. Additionally, the entire balance of the contract is transferred to a specified payout address outlined in the MigrationMsg. Consequently, this action results in the depletion of all funds and the eradication of all state, effectively "burning" the contract.
