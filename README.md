# HelloID-Conn-Prov-Target-Somtoday-Student

> [!IMPORTANT]
> This repository contains the connector and configuration code only. The implementer is responsible to acquire the connection details such as username, password, certificate, etc. You might even need to sign a contract or agreement with the supplier before implementing this connector. Please contact the client's application manager to coordinate the connector requirements.

<p align="center">
  <img src="./Logo.png"
</p>

## Table of contents

- [HelloID-Conn-Prov-Target-Somtoday-Student](#helloid-conn-prov-target-somtoday-student)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Getting started](#getting-started)
    - [Connection settings](#connection-settings)
    - [Correlation configuration](#correlation-configuration)
    - [Available lifecycle actions](#available-lifecycle-actions)
    - [Field mapping](#field-mapping)
  - [Remarks](#remarks)
    - [Configuration](#configuration)
      - [Environment](#environment)
        - [Proxy](#proxy)
      - [Instelling](#instelling)
    - [SchoolName](#schoolname)
    - [leerlingNummer](#leerlingnummer)
    - [AccountReference](#accountreference)
    - [Remote Identifier Comparison](#remote-identifier-comparison)
    - [Logging](#logging)
  - [Development resources](#development-resources)
    - [API endpoints](#api-endpoints)
    - [API documentation](#api-documentation)
  - [Getting help](#getting-help)
  - [HelloID docs](#helloid-docs)

## Introduction

HelloID-Conn-Prov-Target-Somtoday-Student is a target connector that integrates with Somtoday-Student, a system offering REST APIs for programmatic data interaction. This connector is responsible for creating student accounts (referred to as leerlingen) within Somtoday. However, the creation of student records is out of scope. Therefore, a student (leerling) must already exist in Somtoday before an account can be created.

## Getting started

### Connection settings

The following settings are required to connect to the API.

| Setting           | Description                                                         | Mandatory |
| ----------------- | ------------------------------------------------------------------- | --------- |
| ClientID          | The ClientID to connect to the API                                  | Yes       |
| ClientSecret      | The ClientSecret to connect to the API                              | Yes       |
| Instelling        | The name of the organization (instelling or schoolnaam) in Somtoday | Yes       |
| Environment       | The somtoday environment you wish to connect to                     | Yes       |
| UseConnectorProxy | The URL to the API                                                  | Yes       |

> [!WARNING]
> The proxy requires different credentials. Make sure to configure the appropriate credentials for the _Tool4ever_ connector proxy in the configuration settings.

### Correlation configuration

The correlation configuration is used to specify which properties will be used to match an existing account within _Somtoday-Student_ to a person in _HelloID_.

| Setting                   | Value            |
| ------------------------- | ---------------- |
| Enable correlation        | `True`           |
| Person correlation field  | `ExternalId`     |
| Account correlation field | `leerlingNummer` |

Correlation is based on the `leerlingNummer`. During the _create_ lifecycle action, all students (leerlingen) are retrieved. If a leerling with the specified `leerlingNummer` (from `correlationValue`) is found, the process verifies whether that leerling already has an associated account.

> [!NOTE]
> If you also use Somtoday as a source system, you can extend your source person with the leerling `uuid`. Doing so eliminates the need to retrieve all leerlingen, making that step redundant. This requires modifications in the code to handle the `uuid` directly instead of fetching all student data.

> [!WARNING]
> The `leerlingNummer` must be known within _HelloID_ since this its value will be used for correlation.

> [!TIP]
> _For more information on correlation, please refer to our correlation [documentation](https://docs.helloid.com/en/provisioning/target-systems/powershell-v2-target-systems/correlation.html) pages_.

### Available lifecycle actions

The following lifecycle actions are available:

| Action             | Description                                                                     |
| ------------------ | ------------------------------------------------------------------------------- |
| create.ps1         | Creates a new account.                                                          |
| update.ps1         | Updates the attributes of an account.                                           |
| configuration.json | Contains the connection settings and general configuration for the connector.   |
| fieldMapping.json  | Defines mappings between person fields and target system person account fields. |

### Field mapping

The field mapping can be imported by using the _fieldMapping.json_ file.

## Remarks

### Configuration

#### Environment

Within the __configuration__, you can choose which environment you are using: test, acceptance, or production. The connector will automatically use the correct base URL to connect to Somtoday based on the selected environment.

##### Proxy

If you also want to use the _Tool4ever_ connector proxy, make sure to toggle the `UseConnectorProxy` setting in the configuration.

> [!WARNING]
> The proxy requires different credentials. Make sure to configure the appropriate credentials for the _Tool4ever_ connector proxy in the configuration settings.

#### Instelling

The `Instelling` is the name of the organization in Somtoday. An organization (instelling) can have multiple schools. The `schoolName` is determined by the `PrimaryContract.Department.DisplayName`.

### SchoolName

As mentioned [See: Instelling](#instelling), the `schoolName` is determined by the `PrimaryContract.Department.DisplayName`. If, in your environment, the `schoolName` is stored in a different property, make sure to update the code accordingly.

> [!NOTE]
> Accounts will only be created for the schoolName specified.

### leerlingNummer

The `leerlingNummer` is used for correlation wihtin the _create_ lifecycle action. Make sure the `leerlingNummer` is a available wihtin HelloID for the correlation to function correctly. FOr more information, refer to the [correlation section](#correlation-configuration).

### AccountReference

Within the _create_ lifecycle action, both the `leerlingUUD` and `schoolUUID` will be stored within the `accountReference`.

### Remote Identifier Comparison

The remoteidentifier property is a collection of objects (providerType, domain, identifier).
The script checks for missing and newly added identifiers by converting them to JSON strings (compressed for easy comparison).
If any identifiers are removed or added, they are stored in propertiesChanged with "Property" set to "remoteidentifier".

If there are changes, the script prepares an update object (changedPropertiesObject).
- For remoteidentifier, it extracts the first added identifier and stores it as a new object.
- For other properties, it simply assigns the new value.

### Logging

A log message is generated for each updated property.
- For remoteidentifier, it logs the removed and added values in detail.
- For other properties, it logs the old and new values.

## Development resources

### API endpoints

The following endpoints are used by the connector

| Endpoint                             | Description                                         |
| ------------------------------------ | --------------------------------------------------- |
| /connect/vestiging/leerling/account  | Updates and retrieves student account information   |
| /connect/instelling                  | Retrieve instellingen from Somtoday                 |
| /connect/vestiging                   | Retrieve vestigingen from Somtoday                  |
| /inloggen.{environment}/oauth2/token | Retrieve an oAuth token for a specific organization |

### API documentation

https://editor.swagger.io/?url=https://api.somtoday.nl/rest/v1/connect/documented/openapi

## Getting help

> [!TIP]
> _For more information on how to configure a HelloID PowerShell connector, please refer to our [documentation](https://docs.helloid.com/en/provisioning/target-systems/powershell-v2-target-systems.html) pages_.

> [!TIP]
>  _If you need help, feel free to ask questions on our [forum](https://forum.helloid.com/forum/helloid-connectors/provisioning/711-helloid-provisioning-target-somtoday-connectapi-students )_.

## HelloID docs

The official HelloID documentation can be found at: https://docs.helloid.com/
