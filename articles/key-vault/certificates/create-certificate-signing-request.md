---
title: Tworzenie i scalanie CSR w Azure Key Vault
description: Tworzenie i scalanie CSR w Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 9772ea320ff28325ffdc8cdcb6e35947b182d1b3
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081439"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Tworzenie i scalanie CSR w Key Vault

Azure Key Vault obsługuje tworzenie żądania podpisania certyfikatu za pomocą pary kluczy prywatnych i podpisywane przez wybrany urząd certyfikacji. Może to być wewnętrzny urząd certyfikacji przedsiębiorstwa lub zewnętrzny publiczny urząd certyfikacji. Żądanie podpisania certyfikatu (również CSR lub żądanie certyfikacji) to komunikat wysyłany przez użytkownika do urzędu certyfikacji w celu żądania wystawienia certyfikatu cyfrowego.

Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](/azure/key-vault/certificates/about-certificates).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Dodawanie certyfikatu w Key Vault wystawiony przez niezaufany urząd certyfikacji

Poniższe kroki ułatwią utworzenie certyfikatu z urzędów certyfikacji, które nie są partnerskie Key Vault (na przykład GoDaddy nie jest zaufanym urzędem certyfikacji magazynu kluczy) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Najpierw **Utwórz zasady dotyczące certyfikatów**. Key Vault nie zarejestruje ani nie odnowi certyfikatu z wystawcy w imieniu użytkownika, ponieważ urząd certyfikacji wybrany w tym scenariuszu nie jest obsługiwany, w związku z czym wystawca jest ustawiony na nieznany.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Tworzenie **żądania podpisania certyfikatu**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Pobieranie żądania CSR **podpisanego przez urząd certyfikacji** `$certificateOperation.CertificateSigningRequest` jest base4 zakodowane żądanie podpisywania certyfikatu certyfikatu. Ten obiekt BLOB i zrzut można wykonać w witrynie sieci Web żądania certyfikatu wystawcy. Ten krok różni się od urzędu certyfikacji do urzędu certyfikacji, jednak najlepszym sposobem jest wyszukanie wytycznych urzędu certyfikacji dotyczące sposobu wykonywania tego kroku. Możesz również użyć narzędzi, takich jak CertReq lub OpenSSL, aby uzyskać podpisane żądanie certyfikatu i zakończyć proces generowania certyfikatu.


4. **Scalanie podpisanego żądania** w Key Vault po podpisaniu żądania certyfikatu przez wystawcę można przywrócić podpisany certyfikat i scalić go z początkową parą kluczy prywatnych i publicznych utworzonych w Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Żądanie certyfikatu zostało teraz pomyślnie scalone.

### <a name="azure-portal"></a>Azure Portal

1.  Aby wygenerować CSR dla wybranego urzędu certyfikacji, przejdź do magazynu kluczy, do którego chcesz dodać certyfikat.
2.  Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
3.  Wybierz kartę **generowanie/Importowanie** .
4.  Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu:** Utworzenie.
    - **Nazwa certyfikatu:** ContosoManualCSRCertificate.
    - **Typ urzędu certyfikacji:** Certyfikat wystawiony przez niezintegrowany urząd certyfikacji
    - **Temat:**`"CN=www.contosoHRApp.com"`
    - Wybierz inne wartości zgodnie z potrzebami. Kliknij pozycję **Utwórz**.

    ![Właściwości certyfikatu](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Zobaczysz, że certyfikat został teraz dodany na liście certyfikatów. Wybierz ten nowy certyfikat, który został właśnie utworzony. Bieżący stan certyfikatu to "wyłączone", ponieważ nie został jeszcze wystawiony przez urząd certyfikacji.
7. Kliknij kartę **operacja certyfikatu** i wybierz pozycję **Pobierz CSR**.
 ![Właściwości certyfikatu](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Przyjmij plik CSR do urzędu certyfikacji, aby żądanie zostało podpisane.
9.  Gdy żądanie zostanie podpisane przez urząd certyfikacji, Przywróć plik certyfikatu, aby **scalić podpisane żądanie** na tym samym ekranie operacji certyfikatu.

Żądanie certyfikatu zostało teraz pomyślnie scalone.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli certyfikat wystawiony w stanie "Disabled" w Azure Portal, należy wyświetlić **operację certyfikatu** w celu przejrzenia komunikatu o błędzie dla tego certyfikatu.

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
