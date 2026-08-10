---
icon: server
---

# Backend App Development

When there are some backend services that need to directly interact with Verona, we can still provide the corresponding capabilities.

## The different types of Verona integration on backend

When you need to integrate Web3 into your product, you may encounter the following challenges:

* You already have an existing user base and do not want them to change their usage habits.
* You have a mature Web2 frontend user experience and do not want to rewrite most of your frontend code.
* You have existing infrastructure and integrations that you wish to continue using.

Verona provides a variety of ways to integrate, allowing you to choose the approach that best fits your product for Web3 integration.

| Approach                                         | With frontend | With backend | Using MetaAccount(Account Abstraction) + Gasless Transaction | Example Guide                                                                                                             |
| ------------------------------------------------ | ------------- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| Direct Blockchain Interaction                    | No            | Yes          | No                                                           | [Interact with Verona via your Backend Service](backend-using-cosmjs.md)                                               |
| Web2 Experience with Verona Integration in backend | Optional      | Yes          | Yes                                                          | [Account Abstraction for RESTful API with Backend Session](build-restapi-with-account-abstraction-and-backend-session.md) |

