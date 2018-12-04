---
title: Tworzenie pliku odc, aby nawiązać połączenie z serwerem usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć plik połączenia danych pakietu Office, aby nawiązać połączenie i pobieranie danych z serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7798cea053be1662edeb5bc8bfcef00f656b8d6e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832943"
---
# <a name="create-an-office-data-connection-file"></a>Tworzenie pliku połączenia danych pakietu Office

Informacje przedstawione w tym artykule opisano, jak utworzyć plik połączenia danych pakietu Office do nawiązywania połączenia z serwerem usług Azure Analysis Services z programu Excel 2016 numer wersji 16.0.7369.2117 lub wcześniej, lub Excel 2013. Zaktualizowany [dostawcy MSOLAP.7](analysis-services-data-providers.md) jest również wymagany.


1. Skopiuj przykładowy plik połączenia poniżej i Wklej do edytora tekstu. 

2. W `odc:ConnectionString`, Zmień następujące właściwości:

    *   W `Data Source=asazure://<region>.asazure.windows.net/<servername>;` zmienić `<region>` w regionie Twojego serwera usług Analysis Services i `<servername>` do nazwy serwera.

    *   W `Initial Catalog=<database>;` zmienić `<database>` nazwę bazy danych.

3. W `<odc:CommandText>Model</odc:CommandText>` zmienić `Model` nazwę modelu lub perspektywy. 

4. Zapisz plik z `.odc` rozszerzenie C:\Users\\*username*\Documents\My folder źródła danych.

5. Kliknij prawym przyciskiem myszy plik, a następnie kliknij przycisk **Otwórz w programie Excel**. Lub w programie Excel na **danych** wstążki, kliknij przycisk **istniejące połączenia**, wybierz swój plik, a następnie kliknij przycisk **Otwórz**.



**Przykładowy plik połączenia**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```



