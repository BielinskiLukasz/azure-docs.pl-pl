---
title: Zarządzanie usługą Azure Data Lake Analytics za pomocą zestawu Azure SDK dla środowiska Node.js
description: W tym artykule opisano, jak zarządzać kontami, źródłami danych, zadaniami i użytkownikami usługi Data Lake Analytics za pomocą zestawu Azure SDK dla środowiska Node.js.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 3b5b11b148910e9bd1348b20a25fa8383fc2ec9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812739"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Zarządzanie usługą Azure Data Lake Analytics za pomocą zestawu Azure SDK dla środowiska Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano, jak zarządzać kontami, źródłami danych, użytkownikami i zadaniami usługi Azure Data Lake Analytics za pomocą aplikacji napisanych przy użyciu zestawu Azure SDK dla środowiska Node.js. 

Obsługiwane są następujące wersje:
* **Środowisko node.js w wersji: 0.10.0 lub wyższej**
* **Wersja interfejsu API REST dla konta: 2015-10-01-preview**
* **Wersja interfejsu API REST dla katalogu: 2015-10-01-preview**
* **Wersja interfejsu API REST dla zadania: 2016-03-20-preview**

## <a name="features"></a>Funkcje
* Zarządzanie kontami: tworzenie, pobieranie, wyświetlanie, aktualizowanie i usuwanie.
* Zarządzanie zadaniami: przesyłanie, pobieranie, wyświetlanie i anulowanie.
* Zarządzanie katalogami: pobieranie i wyświetlanie.

## <a name="how-to-install"></a>Jak zainstalować
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Tworzenie klienta Data Lake Analytics
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Pobieranie listy zadań
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Pobieranie listy baz danych w katalogu Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Zobacz także
* [Zestaw Microsoft Azure SDK dla środowiska Node.js](https://github.com/azure/azure-sdk-for-node)
* [Zestaw Microsoft Azure SDK dla środowiska Node.js — Data Lake Store Management](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

