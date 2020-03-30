---
title: Samouczek interfejsu API raportowania usługi AD z certyfikatami | Dokumenty firmy Microsoft
description: W tym samouczku wyjaśniono, jak używać interfejsu API raportowania usługi Azure AD z poświadczeniami certyfikatów w celu uzyskania danych z katalogów bez interwencji użytkownika.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014269"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Samouczek: Pobierz dane przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](concept-reporting-api.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. Jeśli chcesz uzyskać dostęp do interfejsu API raportowania usługi Azure AD bez interwencji użytkownika, należy skonfigurować dostęp do używania certyfikatów.

W tym samouczku dowiesz się, jak używać certyfikatu testowego, aby uzyskać dostęp do interfejsu API programu MS Graph do raportowania. Nie zaleca się używania certyfikatów testowych w środowisku produkcyjnym. 

## <a name="prerequisites"></a>Wymagania wstępne

1. Aby uzyskać dostęp do danych logowania, upewnij się, że masz dzierżawę usługi Azure Active Directory z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach. 

2. Utwórz lub przełącz się na konto użytkownika w **roli administratora globalnego,** **administratora zabezpieczeń,** **czytnika zabezpieczeń** lub **czytnika raportów** dla dzierżawy. 

3. Ukończ [wymagania wstępne dostępu do interfejsu API raportowania usługi Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Pobierz i zainstaluj [usługę Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Zainstaluj [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ten moduł zapewnia kilka poleceń cmdlet narzędzi, w tym:
    - Biblioteki ADAL potrzebne do uwierzytelniania
    - Tokeny dostępu użytkownika, kluczy aplikacji i certyfikatów korzystających z bibliotek ADAL
    - Stronicowane wyniki obsługi interfejsu API programu Graph

6. Jeśli po raz pierwszy przy użyciu modułu uruchom **Install-MSCloudIdUtilsModule**, w przeciwnym razie zaimportuj go za pomocą polecenia **Import-Module** Powershell. Sesja powinna wyglądać podobnie do ![tego ekranu: Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Użyj polecenia **Programu Powershell New-SelfSignedCertificate,** aby utworzyć certyfikat testowy.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Użyj polecenia **Eksportuj certyfikat,** aby wyeksportować go do pliku certyfikatu.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami

1. Przejdź do [witryny Azure portal](https://portal.azure.com), wybierz usługę Azure Active **Directory**, a następnie wybierz **pozycję Rejestracje aplikacji** i wybierz aplikację z listy. 

2. Wybierz **pozycję Klawisze ustawień** > **Keys** i wybierz pozycję Przekaż **klucz publiczny**.

3. Wybierz plik certyfikatu z poprzedniego kroku i wybierz pozycję **Zapisz**. 

4. Zanotuj identyfikator aplikacji i odcisk palca certyfikatu, który właśnie zarejestrowałeś w aplikacji. Aby znaleźć odcisk palca, ze strony aplikacji w portalu przejdź do **ustawień** i kliknij przycisk **Klawisze**. Odcisk palca będzie w obszarze **listy klucze publiczne.**

5. Otwórz manifest aplikacji w edytorze manifestów wbudowanych i zastąp właściwość *keyCredentials* nowymi informacjami o certyfikacie przy użyciu następującego schematu. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Zapisz manifest. 
  
7. Teraz można uzyskać token dostępu dla interfejsu API ms graph przy użyciu tego certyfikatu. Użyj polecenia cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** z modułu Programu PowerShell MSCloudIdUtils, przechodząc w identyfikatorze aplikacji i odcisk palca uzyskany z poprzedniego kroku. 

   ![Portal Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Użyj tokenu dostępu w skrypcie programu Powershell do kwerendy interfejsu API wykresu. Użyj polecenia cmdlet **Invoke-MSCloudIdMSGraphQuery** z msCloudIDUtils do wyliczenia znaków i directoryAudits punktu końcowego. To polecenie cmdlet obsługuje wielostronicowe wyniki i wysyła te wyniki do potoku programu PowerShell.

9. Kwerenda directoryAudits punktu końcowego, aby pobrać dzienniki inspekcji. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Kwerenda punkt końcowy signins pobrać dzienniki logowania.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Teraz można wyeksportować te dane do pliku CSV i zapisać je w systemie SIEM. Skrypt można również opakować w zaplanowane zadanie, aby okresowo uzyskiwać dane usługi Azure AD od dzierżawcy bez konieczności przechowywania kluczy aplikacji w kodzie źródłowym. 

## <a name="next-steps"></a>Następne kroki

* [Pierwsze wrażenie dotyczące interfejsów API raportowania](concept-reporting-api.md)
* [Inspekcja odwołania interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odwołanie do interfejsu API raportu aktywności logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
