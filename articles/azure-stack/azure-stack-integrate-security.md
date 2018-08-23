---
title: Usługa Azure przekazywania usługi syslog stosu
description: Dowiedz się, jak zintegrować usługę Azure Stack z rozwiązania do monitorowania przy użyciu przekazywania usługi syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054985"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integracja centrum danych usługi Azure Stack — przekazywania usługi syslog

W tym artykule pokazano, jak zintegrować infrastruktury Azure Stack z rozwiązania zabezpieczenia zewnętrzne wdrożone w Twoim centrum danych za pomocą usługi syslog. Na przykład system zabezpieczeń informacji Event Management (SIEM). Kanał syslog udostępnia inspekcje, alerty i dzienniki zabezpieczeń ze wszystkich składników infrastruktury Azure Stack. Użyj przekazywania usługi syslog, aby zintegrować z rozwiązania do monitorowania zabezpieczeń i/lub pobrać inspekcje, alerty i zabezpieczenia dzienników do ich przechowywania do przechowywania danych. 

Usługi Azure Stack, począwszy od aktualizacji 1805 ma syslog zintegrowanego klienta, który, po skonfigurowaniu emituje komunikaty dziennika systemowego z ładunku w Common Event Format (CEF). 

> [!IMPORTANT]
> Przekazywanie dziennik systemowy jest w wersji zapoznawczej. Go nie powinna być używana po w środowiskach produkcyjnych. 

Na poniższym diagramie przedstawiono główne składniki, które uczestniczą w integracji usługi syslog.

![Diagram przekazywania usługi SYSLOG](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Konfigurowanie funkcji przekazywania usługi syslog

Klient usługi syslog w usłudze Azure Stack obsługuje następujące konfiguracje:

1. **SYSLOG za pośrednictwem protokołu TCP, przy użyciu wzajemnego uwierzytelniania (klient i serwer) i szyfrowania TLS 1.2:** w tej konfiguracji klienta usługi syslog i serwera syslog można sprawdzić tożsamości od siebie przy użyciu certyfikatów. Komunikaty są wysyłane za pośrednictwem szyfrowanego kanału protokołu TLS 1.2.

2. **SYSLOG za pośrednictwem protokołu TCP serwera uwierzytelniania i szyfrowania TLS 1.2:** w tej konfiguracji klienta usługi syslog można sprawdzić tożsamości serwera usługi syslog za pomocą certyfikatu. Komunikaty są wysyłane za pośrednictwem szyfrowanego kanału protokołu TLS 1.2.

3. **SYSLOG za pośrednictwem protokołu TCP z bez szyfrowania:** w tej konfiguracji klienta usługi syslog ani z serwera syslog weryfikuje tożsamość siebie nawzajem. Komunikaty są wysyłane w postaci zwykłego tekstu za pośrednictwem protokołu TCP.

4. **SYSLOG za pośrednictwem protokołu UDP z bez szyfrowania:** w tej konfiguracji klienta usługi syslog ani z serwera syslog weryfikuje tożsamość siebie nawzajem. Komunikaty są wysyłane w postaci zwykłego tekstu za pośrednictwem protokołu UDP.

> [!IMPORTANT]
> Firma Microsoft zdecydowanie zaleca się używać uwierzytelniania i szyfrowania protokołu TCP (Konfiguracja #1 lub, co bardzo minimalne #2) dla środowisk produkcyjnych chronić przed atakami typu man-in--middle i podsłuchiwaniu komunikatów.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Polecenia cmdlet służące do konfigurowania przekazywania usługi syslog
Konfigurowanie funkcji przekazywania usługi syslog wymaga dostępu do uprzywilejowanych punktu końcowego (program ten). Program ten konfigurowania przekazywania usługi syslog dodano dwa polecenia cmdlet programu PowerShell:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parametry polecenia cmdlet

Parametry *SyslogServer zestaw* polecenia cmdlet:

| Parametr | Opis | Typ |
|---------|---------| ---------|
| *ServerName* | Nazwa FQDN lub adres IP serwera syslog | Ciąg |
|*Bez szyfrowania*| Wymuszaj na kliencie i wysłać komunikaty dziennika systemu w postaci zwykłego tekstu | Flaga | 
|*SkipCertificateCheck*| Pomiń sprawdzanie poprawności certyfikatu oferowane przez serwer usługi syslog w trakcie początkowego uzgadniania TLS | Flaga |
|*SkipCNCheck*| Pomiń sprawdzanie poprawności wartości nazwy pospolitej certyfikatu oferowane przez serwer usługi syslog w trakcie początkowego uzgadniania TLS | Flaga |
|*UseUDP*| Za pomocą usługi syslog UDP jako protokół transportowy |Flaga |
|*Usuń*| Usuń konfigurację serwera z klienta, a następnie Zatrzymaj przekazywania usługi syslog| Flaga |

Parametry *SyslogClient zestaw* polecenia cmdlet:
| Parametr | Opis | Typ |
|---------|---------| ---------|
| *pfxBinary* | plik PFX zawierający certyfikat, który ma być używany przez klienta jako tożsamości do uwierzytelniania względem serwera syslog  | Byte[] |
| *CertPassword* |  Hasła do importowania klucza prywatnego, który jest skojarzony z pliku pfx | SecureString |
|*RemoveCertificate* | Usuń certyfikat z klienta | Flaga|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Konfigurowanie przekazywania usługi syslog za pomocą protokołu TCP, wzajemnego uwierzytelniania i szyfrowania TLS 1.2

W tej konfiguracji klienta usługi syslog w usłudze Azure Stack przesyła dalej wiadomości do serwera syslog za pośrednictwem protokołu TCP, za pomocą szyfrowania TLS 1.2. Podczas początkowego uzgadniania klient sprawdza, czy serwer udostępnia nieprawidłowy zaufany certyfikat; Podobnie klient także certyfikatu do serwera jako dowód swojej tożsamości. Ta konfiguracja jest najbardziej bezpieczne w postaci, w jakiej zawiera pełnej weryfikacji tożsamości klienta i serwera i wysyła komunikaty za pośrednictwem szyfrowanego kanału. 

> [!IMPORTANT]
> Firma Microsoft zaleca się użyć tej konfiguracji dla środowisk produkcyjnych. 

Aby skonfigurować przekazywania usługi syslog za pomocą protokołu TCP, wzajemnego uwierzytelniania i szyfrowania TLS 1.2, uruchom oba te polecenia cmdlet:
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
Certyfikat klienta musi mieć takim samym certyfikatem głównym jak podano podczas wdrażania usługi Azure Stack. On również muszą zawierać klucz prywatny.

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Konfigurowanie przekazywania usługi syslog za pomocą protokołu TCP serwera uwierzytelniania i szyfrowania TLS 1.2

W tej konfiguracji klienta usługi syslog w usłudze Azure Stack przesyła dalej wiadomości do serwera syslog za pośrednictwem protokołu TCP, za pomocą szyfrowania TLS 1.2. Podczas uzgadniania początkowej klient sprawdza również, czy serwer udostępnia nieprawidłowy zaufany certyfikat. Zapobiega to klienta do wysyłania komunikatów do niezaufanych miejsc docelowych.
TCP, uwierzytelniania i szyfrowania jest domyślna konfiguracja i przedstawia minimalny poziom zabezpieczeń, które firma Microsoft zaleca w środowisku produkcyjnym. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

W przypadku, gdy chcesz przetestować integrację z serwerem usługi syslog za pomocą klienta usługi Azure Stack przy użyciu certyfikatu z podpisem własnym i/lub niezaufanych, można użyć tych flag do pominięcia weryfikacji serwera wykonywane przez klienta podczas początkowego uzgadniania.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Firma Microsoft zaleca się przed użyciem flagi - SkipCertificateCheck w środowiskach produkcyjnych. 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Konfigurowanie przekazywania usługi syslog przy użyciu protokołu TCP i bez szyfrowania

W tej konfiguracji klienta usługi syslog w usłudze Azure Stack przesyła dalej wiadomości do serwera syslog za pośrednictwem protokołu TCP, za pomocą bez szyfrowania. Klient nie weryfikuje tożsamość serwera, ani nie zapewnia weryfikacji tożsamości na serwer. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> Firma Microsoft zaleca się przed za pomocą tej konfiguracji dla środowisk produkcyjnych. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Konfigurowanie przekazywania usługi syslog za pomocą protokołów UDP i bez szyfrowania

W tej konfiguracji klienta usługi syslog w usłudze Azure Stack przesyła dalej wiadomości do serwera syslog za pośrednictwem protokołu UDP, za pomocą bez szyfrowania. Klient nie weryfikuje tożsamość serwera, ani nie zapewnia weryfikacji tożsamości na serwer. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
W trakcie największa łatwość konfiguracji UDP z bez szyfrowania nie zapewnia żadnej ochrony przed atakami typu man-in--middle i podsłuchiwaniu komunikatów. 

> [!IMPORTANT]
> Firma Microsoft zaleca się przed za pomocą tej konfiguracji dla środowisk produkcyjnych. 


## <a name="removing-syslog-forwarding-configuration"></a>Usuwanie konfiguracji przekazywania usługi syslog

Aby całkowicie usunąć konfigurację serwera usługi syslog i Zatrzymaj przekazywania usługi syslog:

**Usuń konfigurację serwera usługi syslog od klienta**

```PowerShell  
Set-SyslogServer -Remove
```

**Usuń certyfikat klienta z klienta**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Weryfikowanie ustawień usługi syslog

Jeśli klient usługi syslog jest pomyślnie połączono z serwerem usługi syslog, należy szybko rozpocząć odbieranie zdarzeń. Jeśli nie widzisz dowolne zdarzenie, sprawdź konfigurację usługi klienta syslog, uruchamiając następujące polecenia cmdlet:

**Zweryfikuj konfigurację serwera w kliencie usługi syslog**

```PowerShell  
Get-SyslogServer
```

**Sprawdź ustawienia certyfikatu klienta usługi syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schemat komunikatu dziennika systemowego

Przekazywania usługi syslog infrastruktury Azure Stack wysyła komunikaty sformatowane we wspólnych Event Format (CEF).
Każdy komunikat dziennika systemu są skonstruowane na podstawie tego schematu: 

```Syslog
<Time> <Host> <CEF payload>
```

Ładunek w formacie CEF opiera się na strukturze poniżej, ale mapowanie dla każdego pola, w zależności od typu komunikatu (zdarzeń Windows Alert utworzony, Alert zamknięte).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>Mapowanie CEF zdarzeń Windows
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela ważności dla zdarzeń Windows: 
| Wartości ważności CEF | Poziom zdarzenia Windows | Wartość numeryczna |
|--------------------|---------------------| ----------------|
|0|Nie zdefiniowano|Wartości: 0. Wskazuje, dzienniki na wszystkich poziomach|
|10|Krytyczny|Wartości: 1. Wskazuje, dzienniki alert krytyczny|
|8|Błąd| Wartość: 2. Wskazuje, dzienniki dla błędu|
|5|Ostrzeżenie|Wartość: 3. Wskazuje, dzienniki ostrzeżenie|
|2|Informacje|Wartość: 4. Wskazuje, dzienniki, aby uzyskać komunikat informacyjny|
|0|Pełne|Wartość: 5. Wskazuje, dzienniki na wszystkich poziomach|

Niestandardowe rozszerzenia tabeli zdarzeń Windows w usłudze Azure Stack:
| Nazwa rozszerzenia niestandardowe | Przykład zdarzenia Windows | 
|-----------------------|---------|
|MasChannel | System|
|MasComputer | Test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| Pomyślnie przetworzono ustawienia zasad grupy dla użytkownika. Nie wprowadzono żadnych zmian, wykryto od momentu ostatniego pomyślnego przetwarzania zasad grupy.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Sukces inspekcji|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |informacje|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-zasadach grupy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Tworzenie procesu|
|MasUserData|KB4093112!! 5112!! Zainstalowane. 0x0!! WindowsUpdateAgent Xpath: / Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapowanie alerty utworzone CEF
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
Tabela ważność alertów:
| Ważność | Poziom |
|----------|-------|
|0|Nie zdefiniowano|
|10|Krytyczny|
|5|Ostrzeżenie|

Niestandardowe rozszerzenia tabeli alerty utworzone w usłudze Azure Stack:
| Nazwa rozszerzenia niestandardowe | Przykład | 
|-----------------------|---------|
|MasEventDescription|Opis: Konto użytkownika \<TestUser\> została utworzona dla \<TestDomain\>. To potencjalne zagrożenie bezpieczeństwa. — KORYGOWANIE: Skontaktuj się z pomocą techniczną. Obsługa klienta jest wymagany, aby rozwiązać ten problem. Nie należy próbować rozwiązać ten problem, bez ich pomocy. Zanim utworzysz żądanie obsługi, należy uruchomić proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mapowanie CEF zamknięte alerty
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

W poniższym przykładzie pokazano wiadomości usługi syslog z ładunku w formacie CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>Kolejne kroki

[Obsługa zasad](azure-stack-servicing-policy.md)