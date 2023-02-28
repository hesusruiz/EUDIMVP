# Trusted Schemas Registry

## Introduction

Trusted Schemas Registry (TSR) API is based on the [EBSI core service of the same name](https://api-pilot.ebsi.eu/docs/apis/trusted-schemas-registry/latest#/).

It enables the **trusted entities** in the ecosystem to manage the registerd schemas (basically the ones for Veriviable Credentials):

- Register a new schema
- Update a registered schema

It enables the **participants** in the ecosystem to:

- Read and validate registered schemas

The Trusted Schemas Registry is a Domain Specific registry service focusing on registering in it only Data Model and Ontology. The registry is agnostic of any type of schema (it supports whatever scheme the consumer needs to anchor, being it JSON, JSON-LD, XML, etc.).

## Endpoints

### Get a schema

'''
/trusted-schemas-registry/v2/schemas/{schemaId}
'''

