---
title: Integrowanie usługi Key Vault z urzędem certyfikacji DigiCert
description: Jak zintegrować Key Vault z urzędem certyfikacji DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: d558b414bfa27b387a559ff6c8fdb5ada32730fc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185642"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrowanie usługi Key Vault z urzędem certyfikacji DigiCert

Azure Key Vault umożliwia łatwe inicjowanie obsługi i wdrażanie certyfikatów cyfrowych dla sieci oraz zarządzanie nimi oraz zapewnia bezpieczną komunikację dla aplikacji. Certyfikat cyfrowy to elektroniczne poświadczenie umożliwiające potwierdzenie tożsamości w transakcji elektronicznej. 

Użytkownicy magazynu kluczy Azure mogą generować certyfikaty DigiCert bezpośrednio z ich Key Vault. Key Vault zapewnić kompleksowe zarządzanie cyklem życia certyfikatu dla certyfikatów wystawionych przez DigiCert za pomocą zaufanego powiązania Key Vault z urzędem certyfikacji DigiCert.

Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](/azure/key-vault/certificates/about-certificates).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz dysponować następującymi zasobami.
* Magazyn kluczy. Możesz użyć istniejącego magazynu kluczy lub utworzyć nowy, wykonując czynności opisane w jednym z następujących przewodników szybki start:
   - [Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md)
   - [Tworzenie magazynu kluczy za pomocą Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Utwórz magazyn kluczy z Azure Portal](../secrets/quick-create-portal.md).
*   Musisz aktywować konto DigiCert CertCentral. [Utwórz](https://www.digicert.com/account/signup/) konto usługi CertCentral.
*   Uprawnienia na poziomie administratora na kontach.


### <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że masz następujące informacje przydatne na koncie usługi DigiCert CertCentral:
-   Identyfikator konta CertCentral
-   Identyfikator organizacji
-   Klucz interfejsu API

## <a name="adding-certificate-authority-in-key-vault"></a>Dodawanie urzędu certyfikacji w Key Vault 
Po zebraniu powyższych informacji z konta DigiCert CertCentral możesz teraz dodać DigiCert do listy urząd certyfikacji w magazynie kluczy.

### <a name="azure-portal"></a>Azure Portal

1.  Aby dodać urząd certyfikacji DigiCert, przejdź do magazynu kluczy, który chcesz dodać DigiCert. 
2.  Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
3.  Wybierz kartę **urzędy certyfikacji** . ![ Właściwości certyfikatu](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Wybierz opcję **Dodaj** .
 ![Właściwości certyfikatu](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Na ekranie **Tworzenie urzędu certyfikacji** wybierz następujące wartości:
    -   **Nazwa**: Dodaj rozpoznawalną nazwę wystawcy. Przykład DigicertCA
    -   **Dostawca**: wybierz z menu pozycję DigiCert.
    -   **Identyfikator konta**: Wprowadź identyfikator konta DigiCert CertCentral
    -   **Hasło konta**: Wprowadź klucz interfejsu API wygenerowany na koncie usługi DigiCert CertCentral
    -   **Identyfikator organizacji**: wprowadź OrgID zebrane z konta DigiCert CertCentral 
    -   Kliknij przycisk **Utwórz**.
   
6.  Zobaczysz, że DigicertCA został już dodany do listy urzędów certyfikacji.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi przy użyciu poleceń lub skryptów. Usługa Azure hosts Azure Cloud Shell, interaktywnego środowiska powłoki, którego można używać za pomocą Azure Portal w samej przeglądarce.

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Tworzenie **grupy zasobów**

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Tworzenie **Key Vault**

Musisz użyć unikatowej nazwy dla magazynu kluczy. Tutaj "contoso-Magazynname" jest nazwą dla Key Vault w tym przewodniku.

- **Nazwa magazynu** Contoso-Magazynname.
- **Nazwa grupy zasobów** ContosoResourceGroup.
- **Lokalizacja** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Zdefiniuj zmienne dla informacji zebranych z konta DigiCert CertCentral.

- Definiowanie zmiennej **identyfikatora konta**
- Definiowanie zmiennej **identyfikatora organizacji**
- Definiowanie zmiennej **klucza interfejsu API**
- Zdefiniuj zmienną **nazwy wystawcy**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzureKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. Ustawianie **wystawcy**. Spowoduje to dodanie DigiCert jako urzędu certyfikacji w magazynie kluczy.
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. **Ustawianie zasad dla certyfikatu i wystawianie certyfikatu** z DigiCert bezpośrednio w Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certyfikat został teraz pomyślnie wystawiony przez urząd certyfikacji DigiCert wewnątrz określonego Key Vault za pomocą tej integracji.

## <a name="troubleshoot"></a>Rozwiąż problemy

Jeśli certyfikat wystawiony w stanie "Disabled" w Azure Portal, należy wyświetlić **operację certyfikatu** w celu przejrzenia komunikatu o błędzie DigiCert dla tego certyfikatu.

 ![Właściwości certyfikatu](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

- Czy mogę wygenerować certyfikat DigiCert z symbolem wieloznacznym za pomocą magazynu kluczy? 
   Tak. Zależą od tego, jak skonfigurowano konto usługi DigiCert.
- Jeśli udało nam się utworzyć certyfikat EV, w jaki sposób należy określić? 
   Podczas tworzenia certyfikatu kliknij pozycję Konfiguracja zasad zaawansowanych, a następnie określ typ certyfikatu. Obsługiwane są następujące wartości: OV-SSL, EV-SSL
- Czy istnieje opóźnienie podczas tworzenia certyfikatu DigiCert poprzez integrację a uzyskanie certyfikatu bezpośrednio przez DigiCert?
   Nie. Podczas tworzenia certyfikatu jest to proces weryfikacji, który może zająć trochę czasu, i weryfikacja zależy od DigiCert procesu.


## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
