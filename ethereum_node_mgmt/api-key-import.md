# API key import

The role supports importing keystores using the Key manager API. This is a standard Ethereum API supported by all clients. In most cases this is the preferred way to import keys. It allows importing the keys along with the slashing protection DB.  This requires that the Validaotr API are enabled - refer to [slindnode.ethereum role documentation](http://localhost:5000/s/Ib9tX0kORM9rMi1DWQvF/enabling-validator-client-api) on how to do it. The tasks will dynamically retrieve the authentication token and use it for the API call.&#x20;

There are two separate tasks:

* Import keystores without slashing protection db (requires [keystores\_without\_slashing\_protection](role-variables.md#keystores\_without\_slashing\_protection) variable)&#x20;
* Import keystores with slashing protection db (requires [keystores\_with\_slashing\_protection](role-variables.md#keystores\_with\_slashing\_protection) variable)

As the nameing implies one is used to import keys without slashing protection DB and other with it. Implementing this kind of logic in Ansible is cumbersome and separating it in two tasks makes them simpler.&#x20;
