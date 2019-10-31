---
title: Łączenie z serwerami Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie i pobrać dane z serwera Analysis Services na platformie Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147319"
---
# <a name="connecting-to-servers"></a>Łączenie z serwerami

W tym artykule opisano nawiązywanie połączenia z serwerem za pomocą aplikacji do modelowania i zarządzania danymi, takich jak SQL Server Management Studio (SSMS) lub Visual Studio z projektami Analysis Services lub z aplikacjami raportowania klienta, takimi jak Microsoft Excel, Power BI Aplikacje klasyczne lub niestandardowe. Połączenia z Azure Analysis Services korzystają z protokołu HTTPS.

## <a name="client-libraries"></a>Biblioteki klienckie

[Pobierz najnowsze biblioteki klienckie](analysis-services-data-providers.md)

Wszystkie połączenia z serwerem, niezależnie od typu, wymagają zaktualizowanych bibliotek klienta biblioteki AMO, ADOMD.NET i OLEDB do łączenia się z serwerem Analysis Services i interfejsem. W przypadku programów SSMS, Visual Studio, Excel 2016 i nowszych oraz Power BI najnowsze biblioteki klienckie są instalowane lub aktualizowane za pomocą comiesięcznych wersji. Jednak w niektórych przypadkach możliwe, że aplikacja może nie mieć najnowszej wersji. Na przykład gdy zasady opóźnią aktualizacje lub aktualizacje pakietu Office 365 są w odłożonym kanale.

## <a name="server-name"></a>Nazwa serwera

Podczas tworzenia serwera Analysis Services na platformie Azure należy określić unikatową nazwę i region, w którym ma zostać utworzony serwer. Podczas określania nazwy serwera w ramach połączenia schemat nazewnictwa serwera to:

```
<protocol>://<region>/<servername>
```
 Gdzie protokół jest ciągiem **asazure**, region jest identyfikatorem URI, w którym został utworzony serwer (na przykład westus.asazure.Windows.NET), a ServerName to nazwa unikatowego serwera w regionie.

### <a name="get-the-server-name"></a>Pobierz nazwę serwera

W **Azure Portal** > Server > **Przegląd** > **Nazwa serwera**, Skopiuj całą nazwę serwera. Jeśli inni użytkownicy w organizacji nawiązują połączenie z tym serwerem, możesz udostępnić tę nazwę serwera. Podczas określania nazwy serwera należy użyć całej ścieżki.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokół dla regionu Wschodnie stany USA 2 to **aspaaseastus2**.

## <a name="connection-string"></a>Parametry połączenia

Podczas nawiązywania połączenia z Azure Analysis Services przy użyciu modelu obiektów tabelarycznych należy użyć następujących formatów parametrów połączenia:

###### <a name="integrated-azure-active-directory-authentication"></a>Zintegrowane uwierzytelnianie Azure Active Directory

Uwierzytelnianie zintegrowane pobiera Azure Active Directory pamięć podręczną poświadczeń, jeśli jest dostępna. W przeciwnym razie zostanie wyświetlone okno logowanie do platformy Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory uwierzytelniania przy użyciu nazwy użytkownika i hasła

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Uwierzytelnianie systemu Windows (zabezpieczenia zintegrowane)

Użyj konta systemu Windows z uruchomionym bieżącym procesem.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Nawiązywanie połączenia przy użyciu pliku odc

W przypadku starszych wersji programu Excel użytkownicy mogą łączyć się z serwerem Azure Analysis Services przy użyciu pliku połączenia danych pakietu Office (odc). Aby dowiedzieć się więcej, zobacz [Tworzenie pliku połączenia danych pakietu Office (odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Następne kroki

[Łączenie z programem Excel](analysis-services-connect-excel.md)    
[Nawiązywanie połączenia z Power BI](analysis-services-connect-pbi.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

