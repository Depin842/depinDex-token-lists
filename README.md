# Depin-Dex token-lists (beta)

This package includes a JSON schema for token lists, and TypeScript utilities for working with token lists.

The JSON schema represents the technical specification for a token list which can be used in a dApp interface.

## What are token lists?

Depin-Dex Token Lists is a specification for lists of token metadata (e.g. address, decimals, ...) that can be used by any dApp interfaces that needs one or more lists of tokens.

Anyone can create and maintain a token list, as long as they follow the specification.

Specifically an instance of a token list is a [JSON](https://www.json.org/json-en.html) blob that contains a list of 
[ERC20](https://github.com/ethereum/eips/issues/20) token metadata for use in dApp user interfaces.


## Validating token lists

This package does not include code for token list validation. You can easily do this by including a library such as 
[ajv](https://ajv.js.org/) to perform the validation against the JSON schema. The schema is exported from the package
for ease of use.

```typescript

import { schema } from 'depin-dex-token-lists'
import Ajv from 'ajv'
import addFormats from 'ajv-formats'
import fetch from 'node-fetch'

const ARBITRUM_LIST = 'https://bridge.arbitrum.io/token-list-42161.json'

async function validate() {
  const ajv = new Ajv({ allErrors: true, verbose: true })
  addFormats(ajv)
  const validator = ajv.compile(schema);
  const response = await fetch(ARBITRUM_LIST)
  const data = await response.json()
  const valid = validator(data)
  if (valid) {
    return valid
  }
  if (validator.errors) {
    throw validator.errors.map(error => {
      delete error.data
      return error
    })
  }
}

validate()
  .then(console.log("Valid List."))
  .catch(console.error)

