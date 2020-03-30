---
title: Funkcje danych klientów usługi Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Dowiedz się więcej o danych klientów w magazynie kluczy
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 67e1aeab4211249075b51bd0138d7875756a3483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883321"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkcje danych klientów usługi Azure Key Vault

Usługa Azure Key Vault odbiera dane klientów podczas tworzenia lub aktualizowania magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu. Te dane klienta są bezpośrednio widoczne w witrynie Azure portal i za pośrednictwem interfejsu API REST. Dane klienta można edytować lub usuwać, aktualizując lub usuwając obiekt zawierający dane.

Dzienniki dostępu do systemu są generowane, gdy użytkownik lub aplikacja uzyskuje dostęp do usługi Key Vault. Szczegółowe dzienniki dostępu są dostępne dla klientów korzystających z usługi Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klientów

Następujące informacje identyfikują dane klientów w usłudze Azure Key Vault:

- Zasady dostępu dla usługi Azure Key Vault zawierają identyfikatory obiektów reprezentujące użytkowników, grupy lub aplikacje
- Podmioty certyfikatów mogą zawierać adresy e-mail lub inne identyfikatory użytkowników lub organizacji
- Kontakty certyfikatów mogą zawierać adresy e-mail, nazwy lub numery telefonów użytkowników
- Wystawcy certyfikatów mogą zawierać adresy e-mail, nazwy, numery telefonów, poświadczenia konta i dane organizacji
- Do obiektów w usłudze Azure Key Vault można zastosować dowolne znaczniki. Obiekty te obejmują magazyny, klucze, wpisy tajne, certyfikaty i konta magazynu. Używane znaczniki mogą zawierać dane osobowe
- Dzienniki dostępu usługi Azure Key Vault zawierają identyfikatory obiektów, [sieci UPn](../active-directory/hybrid/plan-connect-userprincipalname.md)i adresy IP dla każdego wywołania interfejsu API REST
- Dzienniki diagnostyczne usługi Azure Key Vault mogą zawierać identyfikatory obiektów i adresy IP dla wywołań interfejsu API REST

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Te same interfejsy API REST, środowisko portalu i pakiety SDK używane do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu są również w stanie zaktualizować i usunąć te obiekty.

Usuwanie nietrwałe umożliwia odzyskanie usuniętych danych przez 90 dni po usunięciu. W przypadku korzystania z usuwania nietrwałego dane mogą zostać trwale usunięte przed upływem 90-dniowego okresu przechowywania przez wykonanie operacji czyszczenia. Jeśli magazyn lub subskrypcja została skonfigurowana do blokowania operacji przeczyszczania, nie jest możliwe trwałe usunięcie danych, dopóki nie minie zaplanowany okres przechowywania.

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Te same interfejsy API REST, środowisko portalu i skusie SDK, które są używane do tworzenia magazynów, kluczy, wpisów tajnych, certyfikatów i zarządzanych kont magazynu, umożliwiają również wyświetlanie i eksportowanie tych obiektów.

Rejestrowanie dostępu usługi Azure Key Vault jest opcjonalną funkcją, którą można włączyć w celu generowania dzienników dla każdego wywołania interfejsu API REST. Te dzienniki zostaną przeniesione na konto magazynu w ramach subskrypcji, w którym zastosujesz zasady przechowywania, które spełniają wymagania organizacji.

Dzienniki diagnostyczne usługi Azure Key Vault zawierające dane osobowe można pobrać, wysyłając żądanie eksportu w portalu prywatność użytkowników. To żądanie musi zostać złożone przez administratora dzierżawy.

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie magazynu kluczy platformy Azure](key-vault-logging.md)

- [Azure Key Vault — omówienie usuwania nietrwałego](key-vault-soft-delete-cli.md)

- [Operacje klucza usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operacje tajne usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certyfikaty i zasady usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Operacje na koncie magazynu usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
