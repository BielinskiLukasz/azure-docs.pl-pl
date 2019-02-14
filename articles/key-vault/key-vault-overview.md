---
title: Omówienie usługi Azure Key Vault — Azure Key Vault | Microsoft Docs
description: Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 474148289bb03088bbf92eeadc4049ce19625e3e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115950"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:

- **Zarządzanie wpisami tajnymi** — usługa Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami** — usługa Azure Key Vault może być także używana jako rozwiązanie do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- **Zarządzanie certyfikatami** — usługa Azure Key Vault pozwala również łatwo aprowizować i wdrażać prywatne i publiczne certyfikaty Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użycia z platformą Azure i połączonymi zasobami wewnętrznymi oraz zarządzać tymi certyfikatami. 
- **Wpisy tajne w magazynie wspierane przez moduły HSM (Hardware Security Module)** — wpisy tajne i klucze mogą być chronione przez oprogramowanie lub moduły HSM zweryfikowane pod kątem zgodności ze standardem FIPS 140-2 poziom 2.

## <a name="why-use-azure-key-vault"></a>Jakie są zalety korzystania z usługi Azure Key Vault?

### <a name="centralize-application-secrets"></a>Scentralizowana obsługa wpisów tajnych aplikacji

Centralny magazyn wpisów tajnych aplikacji w usłudze Azure Key Vault umożliwia kontrolowanie ich dystrybucji. Usługa Key Vault znacznie ogranicza prawdopodobieństwo przypadkowego ujawnienia wpisów tajnych. Korzystając z usługi Key Vault, deweloperzy aplikacji nie muszą już przechowywać informacji zabezpieczeń w aplikacji. Ponieważ nie trzeba już przechowywać informacji o zabezpieczeniach w aplikacjach, nie ma potrzeby używania tych informacji w kodzie. Na przykład aplikacja może potrzebować połączenia z bazą danych. Zamiast przechowywać parametry połączenia w kodzie aplikacji można je przechowywać bezpiecznie w usłudze Key Vault.

Twoje aplikacje mogą bezpiecznie uzyskiwać dostęp do potrzebnych informacji za pomocą identyfikatorów URI. Te identyfikatory URI umożliwiają aplikacjom pobranie określonych wersji wpisu tajnego. Nie ma konieczności pisania niestandardowego kodu w celu ochrony poufnych informacji przechowywanych w usłudze Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Bezpieczne przechowywanie wpisów tajnych i kluczy

Wpisy tajne i klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM). Używane moduły HSM zostały zweryfikowane pod kątem zgodności ze standardem Federal Information Processing Standards (FIPS) 140-2 Level 2.

Udzielenie dostępu do magazynu kluczy wywołującemu użytkownikowi lub aplikacji wymaga odpowiedniego uwierzytelnienia i autoryzacji. Uwierzytelnianie ustala tożsamość elementu wywołującego, a autoryzacja określa, jakie operacje może on wykonywać.

Uwierzytelnianie jest wykonywane za pośrednictwem usługi Azure Active Directory. Autoryzacja może odbywać się przy użyciu funkcji kontroli dostępu na podstawie ról (RBAC, role-based access control) lub zasad dostępu usługi Key Vault. Funkcja RBAC jest używana do obsługi zarządzania magazynami, a zasady dostępu do magazynu kluczy są używane podczas próby uzyskania dostępu do danych przechowywanych w magazynie.

Usługa Azure Key Vault może być chroniona za pomocą programowego lub sprzętowego modułu HSM. W przypadku sytuacji wymagających dodatkowych zabezpieczeń możesz zaimportować klucz do modułu HSM lub wygenerować w nim klucze, które nigdy nie opuszczają modułu HSM. Firma Microsoft używa sprzętowych modułów zabezpieczeń firmy Thales. Aby przenieść klucz z modułu HSM do usługi Azure Key Vault, można użyć narzędzi firmy Thales.

Usługa Azure Key Vault została zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje dane ani nie mogła ich wyodrębnić.

### <a name="monitor-access-and-use"></a>Monitorowanie dostępu i użycia

Po utworzeniu kilku magazynów usługi Key Vault może zajść potrzeba monitorowania sposobu oraz czasu dostępu do kluczy i wpisów tajnych. Działanie można monitorować przez włączenie rejestrowania dla magazynów. Usługę Azure Key Vault można skonfigurować w następujących celach:

- Archiwizowanie na koncie magazynu.
- Przesyłanie strumieniowe do centrum zdarzeń.
- Wysyłanie dzienników do usługi Log Analytics.

Kontrolujesz dzienniki i możesz je zabezpieczyć przez ograniczenie dostępu, a także możesz usunąć dzienniki, które nie są już potrzebne.

### <a name="simplified-administration-of-application-secrets"></a>Uproszczone administrowanie wpisami tajnymi aplikacji

W przypadku przechowywania cennych danych należy wykonać kilka czynności. Informacje zabezpieczające muszą być bezpieczne, podlegać procesom cyklu życia i być łatwo dostępne. Usługa Azure Key Vault upraszcza proces spełnienia tych wymagań przez:

- Brak konieczności posiadania wiedzy z zakresu sprzętowych modułów zabezpieczeń w firmie.
- Skalowanie w górę w krótkim czasie w celu spełnienia nagłego zapotrzebowania organizacji.
- Replikowanie zawartości usługi Key Vault w regionie do regionu pomocniczego. Replikacja danych zapewnia wysoką dostępność i eliminuje konieczność wykonywania jakichkolwiek czynności przez administratora w celu wyzwolenia trybu failover.
- Udostępnianie standardowych opcji administrowania platformą Azure za pośrednictwem portalu, interfejsu wiersza polecenia platformy Azure i programu PowerShell.
- Zautomatyzowanie pewnych zadań związanych z certyfikatami kupowanymi od publicznych urzędów certyfikacji, na przykład ich rejestracji i odnawiania.

Usługa Azure Key Vault umożliwia także rozdzielenie wpisów tajnych aplikacji. Aplikacje mają dostęp tylko do magazynu, do którego zezwolono im na dostęp, i mogą zostać ograniczone do wykonywania tylko określonych operacji. Istnieje możliwość utworzenia usługi Azure Key Vault dla aplikacji i ograniczenia użycia wpisów tajnych przechowywanych w usłudze Key Vault do konkretnej aplikacji i konkretnego zespołu deweloperów.

### <a name="integrate-with-other-azure-services"></a>Integracja z innymi usługami platformy Azure

Jako bezpieczny magazyn na platformie Azure usługa Key Vault jest używana do upraszczania takich scenariuszy jak:
-  [Usługa Azure Disk Encryption](../security/azure-security-disk-encryption.md)
-  Funkcja [Zawsze szyfrowane]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) na platformie SQL Server i w usłudze Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). 

Samą usługę Key Vault można zintegrować z kontami magazynu, centrami zdarzeń i usługą Log Analytics.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: tworzenie usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia](quick-create-cli.md)
- [Konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault](tutorial-web-application-keyvault.md)
