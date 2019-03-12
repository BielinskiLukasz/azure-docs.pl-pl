---
title: Wersje usługi Key Vault
description: Różne wersje usługi Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b7e3aca133e2e9614ab83be83c20a4dbc2ae5fe2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542390"
---
# <a name="key-vault-versions"></a>Wersje usługi Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - kluczy zarządzanego konta magazynu

Lata 2017 — ułatwia integrację z usługą Azure Storage dodawana funkcja kluczy konta magazynu. Zobacz więcej informacji można znaleźć w temacie omówienie [zarządzanych kluczy konta magazynu — omówienie](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - Soft-delete

Lata 2017 r. — Funkcja usuwania nietrwałego dodane do ochrony danych ulepszone magazynów kluczy i kluczy obiektów. Zobacz więcej informacji można znaleźć w temacie omówienie [omówienie usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - zarządzania certyfikatami

Zarządzanie certyfikatami jest dodawany jako funkcję do Ogólnodostępnej wersji 2015-06-01 26 września 2016 r.

## <a name="2015-06-01---general-availability"></a>2015-06-01 — ogólna dostępność

Wersja ogólnie dostępna w wersji 2015-06-01, ogłosiła 24 czerwca 2015 r.

W tej wersji wprowadzono następujące zmiany:

- Usuń klucz — "use" pola usunięte.
- Uzyskaj informacje o kluczu — "use" pola usunięte.
- Importuj klucz do magazynu — "use" pola usunięte.
- Przywróć klucz — "use" pola usunięte.
- Zmienione "RSA_OAEP" do "RSA-OAEP" algorytmy RSA. Zobacz [o kluczy, wpisów tajnych i certyfikatów](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Drugi (wersja zapoznawcza) w wersji 2015-02-01-preview, ogłosiła 20 kwietnia 2015 r. Aby uzyskać więcej informacji, zobacz [aktualizacji interfejsu API REST](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) wpis w blogu.

Zaktualizowano następujące zadania:

- Utwórz listę kluczy w magazynie — stronicowanie dodano obsługę operacji.
- Utwórz listę wersji klucza — dodano operację, aby wyświetlić listę wersji klucza.
- Utwórz listę kluczy tajnych w magazynie — dodano dzielenia na strony pomocy technicznej.
- Listę wersji klucza tajnego — Dodaj operację, aby wyświetlić listę wersji klucza tajnego.
- Wszystkie operacje — dodano utworzone/zaktualizowane sygnatury czasowe do atrybutów.
- Utwórz klucz tajny - Content-Type są dodawane do wpisów tajnych.
- Utwórz klucz — dodaje znaczniki jako opcjonalne informacje.
- Utwórz klucz tajny - dodane znaczniki jako opcjonalne informacje.
- Zaktualizuj klucz — dodaje znaczniki jako opcjonalne informacje.
- Zaktualizuj klucz tajny - dodane znaczniki jako opcjonalne informacje.
- Zmienić maksymalny rozmiar dotyczących wpisów tajnych z 10 tys bajtów 25 tys. Zobacz, [o kluczy, wpisów tajnych i certyfikatów](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

Pierwszy (wersja zapoznawcza) w wersji 2014-12-08-preview, ogłosiła 8 stycznia 2015 r.

## <a name="see-also"></a>Zobacz także
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
