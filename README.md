# ASCS Smart Contract Library

## Introduction

This is a collection of the smart contracts as used in the ASCS ecosystem. It consists of:

- The user registry
- The Marketplace

The contracts are written for the Tezos blockchain in JSLigo.

## Requirements

- [Ligo](https://ligolang.org/docs/intro/installation?lang=jsligo)
- [Node](https://github.com/nvm-sh/nvm)

## Installation

To install all the requirements run:

```shell
npm install
```

## Compiling the contract

Compiling can be done using the following [Taqueria](https://taqueria.io/) commands

```shell
taq compile registry/registry.jsligo
```

and

```shell
taq compile marketplace/marketplace.jsligo
```

## Running tests

Tests can be ran using the following commands:

```shell
ligo run test --library . ./contracts/registry/registry.test.jsligo
```

and

```shell
ligo run test --library . ./contracts/marketplace/marketplace.test.jsligo
```

## Using Taqueria

There are parameterList and storageList files available that are used by Taqueria. More information on interacting with the contracts using Taqueria can be found on the [Taqueria website](https://taqueria.io/)

## Deploying

There is no standardised deployment procedure currently implemented.

For more information on how to deploy these contracts, visit:

- [Taquito](https://taquito.io/docs/originate/)
- [Better call dev](https://better-call.dev/deploy)
- [Open Tezos](https://docs.tezos.com/smart-contracts/deploying)

## Disclaimer

- Contracts have not been audited and no guarantees are given on their funcionality
- While the contracts are fully functional, the tooling around it including tests and cli interactions are incomplete
- Taqueria is currently in beta and its correctness cannot be fully relied on
