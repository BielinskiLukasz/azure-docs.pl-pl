---
title: "Integracja Azure datacenter stosu - tożsamości"
description: "Dowiedz się, jak zintegrować stosu Azure AD FS z centrum danych usług AD FS"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/20/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: e43b9c7a854bc7150247a2b92d2d37ad6d74c705
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2017
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integracja Azure datacenter stosu - tożsamości

*Dotyczy: Azure stosu zintegrowane systemy*

Stos Azure można wdrożyć przy użyciu usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) jako dostawcy tożsamości. Ten wybór musi być dokonana przed wdrożenia. Wdrożenie za pomocą usług AD FS jest również nazywany wdrażanie stosu Azure w trybie rozłączonym.

W poniższej tabeli przedstawiono różnice między tożsamości dwóch opcji:


||Fizycznie rozłączone|Fizycznie połączone|
|---------|---------|---------|
|Rozliczenia|Musi być pojemności<br> Tylko Enterprise Agreement (EA)|Pojemności lub płatności jako — użytkownik użycia<br>Umowa EA lub dostawca rozwiązań w chmurze (CSP)|
|Tożsamość|Musi być usług AD FS|Azure AD lub AD FS|
|Syndykacja Marketplace|Nie jest obecnie dostępna|Obsługiwane<br>BYOL licencjonowania|
|Rejestracja|Zalecane, wymaga nośnika wymiennego<br> i oddzielne podłączonego urządzenia.|Automatyczne|
|Poprawek i aktualizacji|Wymagane, wymaga nośnika wymiennego<br> i oddzielne podłączonego urządzenia.|Pakiet aktualizacji, które mogą być pobierane bezpośrednio<br> z Internetu do stosu platformy Azure.|

> [!IMPORTANT]
> Nie można przełączyć dostawcy tożsamości bez ponownego wdrożenia całego rozwiązania Azure stosu.

## <a name="active-directory-federation-services-and-graph"></a>Wykres i usług federacyjnych Active Directory

Wdrażanie z usługami AD FS umożliwia tożsamości w istniejącym lesie usługi Active Directory do uwierzytelniania z zasobami w stosie Azure. Ta istniejącego lasu usługi Active Directory wymaga wdrożenia usług AD FS, aby umożliwić tworzenie relacji zaufania federacji usług AD FS.

Uwierzytelnianie jest częścią tożsamości. Aby zarządzać na podstawie dostępu kontroli ról (RBAC) w stosie Azure, musi być skonfigurowany składnik Wykres. Po oddelegowaniu dostęp do zasobu składnika Wykres wyszukuje konta użytkownika w istniejącym lesie usługi Active Directory przy użyciu protokołu LDAP.

![Architektura usługi Azure stosu usług AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Istniejących usług AD FS jest konto usługi tokenu zabezpieczającego (STS) wysyłanej oświadczenia do stosu usługi Azure AD FS (STS zasobu). W stosie Azure automatyzacji tworzy zaufania dostawcy oświadczeń z punktem końcowym metadanych dla istniejących usług AD FS.

W istniejących usług AD FS można skonfigurować zaufanie jednostki uzależnionej. Ten krok nie jest określany przez automatyzację i musi być skonfigurowany przez operatora. Punktu końcowego metadanych stosu Azure opisano w pliku AzureStackStampDeploymentInfo.JSON lub za pośrednictwem uprzywilejowanych punktu końcowego za pomocą polecenia `Get-AzureStackInfo`.

Konfigurację zaufania jednostki uzależnionej strony wymaga skonfigurowania reguły przekształcania oświadczeń, które są obsługiwane przez firmę Microsoft.

W przypadku konfiguracji wykresu konto usługi musi być pod warunkiem, że ma uprawnienie w istniejącej usługi Active Directory do odczytu. To konto jest wymagane jako dane wejściowe dla automatyzacji na potrzeby scenariuszy z RBAC.

Ostatni krok skonfigurowano nowego właściciela subskrypcji dostawcy domyślnego. To konto ma pełny dostęp do wszystkich zasobów, gdy jest zalogowany w usłudze Azure stosu portalu administratora.

Wymagania:


|Składnik|Wymaganie|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|USŁUGI AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Konfigurowanie integracji wykresu

Jako dane wejściowe dla parametrów automatyzacji wymagane są następujące informacje:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomADGlobalCatalog|Nazwa FQDN docelowego lasu usługi Active Directory<br>Czy chcesz zintegrować z|contoso.com|
|CustomADAdminCredentials|Użytkownik z uprawnieniami odczytu LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Utwórz konta użytkowników w istniejącej usługi Active Directory (opcjonalnie)

Opcjonalnie można utworzyć konto usługi wykresu w istniejącej usługi Active Directory. Wykonaj ten krok, jeśli nie masz już konto, którego chcesz używać.

1. W istniejącej usługi Active Directory należy utworzyć następujące konto użytkownika (zalecane):
   - Nazwa użytkownika: graphservice
   - Hasło: należy używać silnych haseł<br>Skonfiguruj hasło nigdy nie wygasa.

   Nie specjalne uprawnienia członkostwo jest wymagane ani

**Wyzwalacz automatyzacji do konfigurowania wykresu**

Do wykonania tej procedury należy użyć komputera w sieci centrum danych, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure.

2. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator) i łączyć się z adresem IP uprzywilejowanych punktu końcowego. Użyj poświadczeń dla CloudAdmin do uwierzytelniania.

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenia. Po wyświetleniu monitu podaj poświadczenia dla konta użytkownika, którego chcesz używać usługi wykresu (na przykład graphservice).

   `Register-DirectoryService -CustomADGlobalCatalog contoso.com`

   > [!IMPORTANT]
   > Poczekaj, aż wyskakujących poświadczeń (Get-Credential nie jest obsługiwana w punkcie końcowym uprzywilejowanych), a następnie wprowadź poświadczenia konta usługi wykresu.

**Wykres protokoły i porty**

Graph usługi Azure stosu używane następujące protokoły i porty do komunikowania się z elementem docelowym usługi Active Directory:


|Typ|Port|Protokół|
|---------|---------|---------|
|LDAP|389|TCP I UDP|
|PROTOKÓŁ LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|PROTOKÓŁ LDAP SSL WYKAZU GLOBALNEGO|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Konfigurowanie integracji usług AD FS, pobierając metadanych Federacji

Wymagane są następujące informacje jako dane wejściowe dla parametrów automatyzacji:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. <cr>Wygląda w ten sposób na strony docelowej usług AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Łącze metadanych Federacji|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalacz automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure. Oczekuje się, że certyfikat używany przez konto usług AD FS usługi STS jest zaufany przez stos Azure.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie połączyć z punktem końcowym uprzywilejowanych.

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenie z parametrami, które są odpowiednie dla danego środowiska:

   `Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml`

3. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów w danym środowisku:

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"`

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Konfigurowanie integracji usług AD FS, zapewniając pliku metadanych Federacji

Użyj tej metody, jeśli jest spełniony jeden z następujących warunków:

- Łańcuch certyfikatów jest inny dla usług AD FS w porównaniu do wszystkich innych punktów końcowych w stosie Azure.
- Nie ma żadnej łączności sieciowej do istniejącego serwera usług AD FS z wystąpienia usług AD FS Azure stosu.

Wymagane są następujące informacje jako dane wejściowe dla parametrów automatyzacji:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. Wygląda na to w ten sposób na strony docelowej usług AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Plik metadanych Federacji|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|

### <a name="create-federation-metadata-file"></a>Utwórz plik metadanych Federacji

Aby wykonać poniższą procedurę należy użyć komputera, który ma łączność sieciową do istniejącego wdrożenia usług AD FS, która staje się kontem usługi STS. Ponadto można zainstalować wymagane certyfikaty.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie, korzystając z parametrów w danym środowisku:

   ```
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Skopiuj plik metadanych do udziału, która jest dostępna z uprzywilejowanym punktu końcowego.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalacz automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie połączyć z punktem końcowym uprzywilejowanych.

   ```
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenie z parametrami, które są odpowiednie dla danego środowiska:

   `Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml`

3. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów w danym środowisku:

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"`

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Skonfiguruj jednostki uzależnionej na istniejącym wdrożeniu usług AD FS (konto usługi STS)

Firma Microsoft udostępnia skrypt, który konfiguruje relację zaufania jednostki uzależnionej, w tym reguły przekształcania oświadczeń. Przy użyciu skryptu jest opcjonalne, jak można ręcznie uruchom polecenia.

Można pobrać skryptu pomocnika z [narzędzi stosu Azure](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) w witrynie Github.

Jeśli zdecydujesz się ręcznie uruchom polecenia, wykonaj następujące kroki:

1. Skopiuj następującą zawartość do pliku txt (na przykład zapisane jako c:\ClaimRules.txt) w sieci centrum danych Członkowskim wystąpienia lub farmy usług AD FS:

   ```
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Aby włączyć uwierzytelnianie oparte na formularzach systemu Windows, otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień użytkownika, a następnie uruchom następujące polecenie:

   `Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")`

3. Aby dodać zaufanie jednostki uzależnionej, uruchom następujące polecenie programu Windows PowerShell na wystąpienie usług AD FS lub element członkowski farmy. Upewnij się, że można zaktualizować punktu końcowego usług AD FS, a następnie wskaż plik utworzony w kroku 1.

   **Dla usług AD FS 2016**

   `Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"`

   **Dla programu AD FS 2012/2012 R2**

   `Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true`

   > [!IMPORTANT]
   > Przystawka programu MMC programu AD FS należy użyć do konfigurowania reguł autoryzacji wystawiania, korzystając z systemu Windows Server 2012 lub 2012 R2 AD FS.

4. Użycie programu Internet Explorer lub przeglądarki Edge można uzyskać dostępu do stosu Azure, należy zignorować tokenu powiązania. W przeciwnym razie próby logowania się niepowodzeniem. Wystąpienie usług AD FS lub element członkowski farmy uruchom następujące polecenie:

   `Set-AdfsProperties -IgnoreTokenBinding $true`

## <a name="spn-creation"></a>Tworzenie nazwy SPN

Istnieje wiele scenariuszy, które korzystają z główną nazwę usługi (SPN) dla uwierzytelniania. Poniżej przedstawiono kilka przykładów:
- Sposób użycia interfejsu wiersza polecenia z wdrożeniem usług AD FS Azure stosu
- Pakiet administracyjny programu System Center dla stosu Azure podczas wdrażania z usługami AD FS
- Dostawcy zasobów Azure stosu podczas wdrażania z usługami AD FS
- Różne aplikacje
- Wymagaj logowania nieinterakcyjnego

Aby uzyskać więcej informacji na temat tworzenia głównej nazwy usługi, zobacz [Tworzenie nazwy głównej usługi dla usług AD FS](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="configuration-rollback"></a>Wycofywanie konfiguracji

Jeśli wystąpi błąd powodujący, że środowisko w stanie, gdy nie będzie można uwierzytelniać, opcja wycofywania jest dostępna.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   `Reset-DatacenterIntegationConfiguration`

   Po uruchomieniu akcji wycofywania, wszystkie zmiany konfiguracji zostały wycofane. Możliwe jest tylko uwierzytelnianie przy użyciu wbudowanych użytkownika "CloudAdmin".

   > [!IMPORTANT]
   > Należy skonfigurować pierwotny właściciel subskrypcji dostawcy domyślnego

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"`

### <a name="collecting-additional-logs"></a>Zbieranie dodatkowych dzienników

Jeśli dowolnych poleceniach cmdlet nie powiedzie się, można zbierać dodatkowe dzienniki przy użyciu `Get-Azurestacklogs` polecenia cmdlet.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   `Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE`


## <a name="next-steps"></a>Następne kroki

[Azure stosu integracji datacenter — publikować punkty końcowe](azure-stack-integrate-endpoints.md)
