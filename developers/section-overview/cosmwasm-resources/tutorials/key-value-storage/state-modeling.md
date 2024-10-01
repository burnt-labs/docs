# State Modeling

The design of key-value storage can appear challenging to individuals with a background in SQL. Even though databases like MongoDB or other streamlined systems incorporate key-value storage, their libraries shield developers from the underlying intricacies.

This complexity is why comprehending the storage system in Cosmos-SDK might not come easily initially. Nevertheless, once you grasp the concept, it becomes straightforward.

When implementing a state model, it's essential to pause and consider some questions before commencing the implementation. For instance:

Do you truly need to persist that data in the blockchain state?

Is the connection genuinely indispensable, or could it be handled by an off-chain database collector for the UI?

By posing these inquiries, you can sidestep unnecessary data writes to the state and prevent excessive storage utilization, ultimately resulting in more cost-effective execution.

#### Tutorial Introduction

This tutorial guides you through the process of crafting a state model to address a specific business scenario.&#x20;

The use case is outlined as follows:

* The system encompasses individuals.
* Individuals possess the capability to join various groups.
* Groups have the capacity to encompass multiple individuals.
* Members within these groups can assume distinct roles, including but not limited to admin, super-admin, and regular members, among others.

## Naive Implementation

Below is a design for an any-to-any relation to store data utilizing IDs. Initially, individual data is organized and indexed through an auto-incremented ID:

```
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct Person {
    pub name: String,
    pub age: i32,
    pub membership_ids: Vec<String>
}
pub const PEOPLE: Map<&[u8], Person> = Map::new("people");

```

Within this design, groups are similarly organized and identified through the use of an ID.

```
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct Group {
    pub name: String,
    pub membership_ids: Vec<String>
}
pub const GROUPS: Map<&[u8], Group> = Map::new("groups");

```

The connection between groups and individuals is established through a membership structure.

```
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct Membership {
  pub person_id: String,
  pub group_id: String,
  pub membership_status_id: String
}
pub const MEMBERSHIPS: Map<&[u8], Membership> = Map::new("memberships");
```

The membership status is determined through the utilization of a "status" string field.

```
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct MembershipStatus {
  pub status: String,
  pub membership_ids: Vec<String>
}
pub const MEMBERSHIP_STATUSES: Map<&[u8], MembershipStatus> = Map::new("membership_statuses");

```

## Optimized Implementation

Although utilizing an ID for person identification might initially appear intuitive, it introduces redundancy. IDs essentially serve as values for user identification, yet users are already uniquely identified by their Address. Consequently, it is more advisable to index individuals based on their Address instead of relying on auto-incremented integers.

```
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct Person {
    pub name: String,
    pub age: u8, // changed to u8 since ages are unsigned and 100 years max.
}
// Addr -> Person
pub const PEOPLE: Map<&[u8], Person> = Map::new("people");

```

The "membership\_id" has been eliminated, and the data type "i32" has been replaced with "u8." Optimizing variable types enhances gas efficiency, leading to reduced transaction fees.

## Now for the Group

Since groups lack an address, it is logical to distinguish them through auto-incremented IDs. If uniqueness in group names is desired, it's more suitable to employ the group name as the indexing mechanism.

```
pub const GROUP_COUNTER: Item<u64> = Item::new("group_counter");
#[derive(Serialize, Deserialize, PartialEq, Debug, Clone)]
pub struct Group {
  pub name: String,
}
// u64 ID -> Group
pub const GROUPS: Map<U64Key, Group> = Map::new("groups");
```

When saving a group, it's imperative to obtain and store the auto-incremented ID in the GROUP\_COUNTER item. To execute this logic, it is advisable to encapsulate it within a dedicated function:

```
pub fn next_group_counter(store: &mut dyn Storage) -> StdResult<u64> {
  let id: u64 = GROUP_COUNTER.may_load(store)?.unwrap_or_default() + 1;
  GROUP_COUNTER.save(store, &id)?;
  Ok(id)
}
pub fn save_group(store: &mut dyn Storage, group: &Group) -> StdResult<()> {
  let id = next_group_counter(store)?;
  let key = U64Key::new(id);
  NEW_GROUPS.save(store, key, group)
}
```

### Establishing Relations and Defining Roles

To establish a relationship between groups and individuals, as well as define an individual's role, the following steps are necessary:

1. Create a list of users associated with a specific group.
2. Develop a list of groups to which a particular user belongs.

These tasks can be achieved by constructing secondary indexes.

_It is strongly encouraged to undertake the remaining implementation as a personal exercise._

\
