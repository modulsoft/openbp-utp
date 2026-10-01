# OpenBP HTTP Services for 1C:UTP and 1C:UPP

[🇺🇦 Українська версія](README_UA.md)

## Overview

This repository is intended for a smooth transition from the 1C accounting system (the UTP and UPP configurations) to the OpenBP platform. After installing the corresponding service, 1C will be able to automatically synchronize with OpenBP, ensuring seamless data exchange and gradual migration.

The OpenBP HTTP service provides a RESTful API that exposes 1C documents and catalogs as JSON endpoints. This allows external systems (including OpenBP) to:

* Query and retrieve catalog data (items, organizations, counterparties)
* Create and update documents (goods receipts, arrivals)
* Synchronize catalogs and transactions in real time

## Available service modules

Both modules expose the same API and differ only in the configuration-specific code. Pick the file that matches your configuration:

| Configuration                                    | Module file                                          |
| ------------------------------------------------ | ---------------------------------------------------- |
| 1C:UTP (Trade Enterprise Management)             | [`http-services/UTP.bsl`](http-services/UTP.bsl)     |
| 1C:UPP (Manufacturing Enterprise Management)     | [`http-services/UPP.bsl`](http-services/UPP.bsl)     |

## Features

* ✅ RESTful API with JSON request/response format
* ✅ CORS support for web application integration
* ✅ Authentication via the 1C infobase user (HTTP Basic Auth)
* ✅ Support for goods receipt documents (ПоступлениеТоваровУслуг)
* ✅ Operations with item catalogs and organizational data
* ✅ Automatic posting and document validation

## Prerequisites

Before installing this HTTP service, make sure you have:

1. **1C:Enterprise platform** version 8.3 or higher
2. **1C:UTP or 1C:UPP configuration**
3. **Required 1C modules/subsystems**:

   * `ОбработкаJSON` - JSON processing module
   * `УправлениеПользователями` - user management module
   * `БухгалтерскийУчет` - accounting module
   * `УправлениеВзаиморасчетами` - mutual settlements management
4. **Administrator rights** for the 1C configuration
5. **Ability to publish HTTP services** enabled in the infobase

## Installation

### Step 1: Creating the HTTP Service

1. Open your 1C:UTP or 1C:UPP configuration in **Configurator mode**

2. In the Configurator, navigate to: **General → HTTP Services** (Общие → HTTP-сервисы)

3. Create a new HTTP service with the following properties:

   * **Name**: `openbp`
   * **Root URL**: `openbp` (or your preferred path)
   * **Description**: "OpenBP integration service"

4. Copy the entire contents of the module matching your configuration: [`http-services/UTP.bsl`](http-services/UTP.bsl) for 1C:UTP or [`http-services/UPP.bsl`](http-services/UPP.bsl) for 1C:UPP

5. Paste it into the HTTP service module editor

6. Add **URL Templates** for each operation:

   | URL Template        | HTTP Method | Handler Function            |
   | ------------------- | ----------- | --------------------------- |
   | `/documents`        | POST        | `ДокументыИзменитьДанные`   |
   | `/documents`        | OPTIONS     | `Options`                   |
   | `/catalogs`         | GET         | `СправочникиПолучитьДанные` |
   | `/catalogs`         | POST        | `СправочникиИзменитьДанные` |
   | `/catalogs`         | OPTIONS     | `Options`                   |
   | `/reports`          | GET         | `reportsCSVПолучитьДанные`  |
   | `/head`             | ANY         | `head`                      |

   > The `/reports` template is available only in the 1C:UTP module.

7. Save the HTTP service configuration

### Step 2: Updating and Publishing the Configuration

1. **Update the database configuration**: Configuration → Update database configuration
2. Resolve any conflicts or errors if they occur
3. Restart 1C:Enterprise in **Configurator mode as Administrator**
4. **Publish the HTTP service**:

   * Open: Administration → Publish on web server
   * Enable publishing for the `openbp` service
   * Note the published URL (usually: `http://your-server:port/your-base/hs/openbp/`)

### Step 3: Configuring User Access

1. Create or identify an infobase user for API access (Administration → Users) and set a login and password for it
2. Make sure the infobase user is linked to an item of the **Пользователи** catalog. The service takes the user from the session (`ПараметрыСеанса.ТекущийПользователь`): it is set as the responsible person in created documents, and the user's default settings (organization, warehouse, units of measure, etc.) are used for filling
3. Make sure the user has the appropriate permissions:

   * Read/write access to catalogs: Counterparties, Items, Organizations, Warehouses
   * Read/write access to documents: ПоступлениеТоваровУслуг
   * Permission to post documents

## License

The code of this repository is completely open and free. MIT License
