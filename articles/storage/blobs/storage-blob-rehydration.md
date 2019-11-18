---
title: Przeodwodnione dane obiektów blob z warstwy Archiwum
description: Zapisz obiekty blob z magazynu archiwum, aby uzyskać dostęp do danych.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113707"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Przeodwodnione dane obiektów blob z warstwy Archiwum

Gdy obiekt BLOB znajduje się w warstwie dostępu archiwizowania, jest traktowany jako przełączony do trybu offline i nie można go odczytać ani modyfikować. Metadane obiektu BLOB pozostają w trybie online i są dostępne, umożliwiając wyświetlenie listy obiektów blob i jego właściwości. Odczytywanie i modyfikowanie danych obiektów BLOB jest możliwe tylko w przypadku warstw online, takich jak gorąca lub chłodna. Dostępne są dwie opcje pobierania i uzyskiwania dostępu do danych przechowywanych w warstwie dostępu archiwizowania.

1. [Ponownie Zapisz zarchiwizowany obiekt BLOB w warstwie online](#rehydrate-an-archived-blob-to-an-online-tier) — przeciąganie obiektu BLOB archiwalnego na gorącą lub chłodną przez zmianę jego warstwy przy użyciu operacji [ustawiania warstwy obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Kopiuj zarchiwizowany obiekt BLOB do warstwy online](#copy-an-archived-blob-to-an-online-tier) — Utwórz nową kopię obiektu BLOB archiwum przy użyciu operacji [copy BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Określ inną nazwę obiektu BLOB i warstwę docelową gorącą lub chłodną.

 Aby uzyskać więcej informacji o warstwach, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Przeciąganie zarchiwizowanego obiektu BLOB do warstwy online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiuj zarchiwizowany obiekt BLOB do warstwy online

Jeśli nie chcesz ponownie odwodnionić obiektu BLOB archiwum, możesz wykonać operację [kopiowania obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Oryginalny obiekt BLOB pozostanie niezmodyfikowany w archiwum, podczas gdy nowy obiekt BLOB zostanie utworzony w warstwie gorąca lub chłodna w trybie online, na którym będziesz pracować. W operacji kopiowania obiektu BLOB można również ustawić opcjonalną Właściwość *x-MS---* --------------.

Obiekty blob archiwalne mogą być kopiowane tylko do warstwy docelowej online w ramach tego samego konta magazynu. Kopiowanie obiektu BLOB archiwum do innego obiektu BLOB archiwum nie jest obsługiwane.

Kopiowanie obiektu BLOB z archiwum może zająć kilka godzin, w zależności od wybranego priorytetu rehydratacji. W tle operacja **kopiowania obiektu BLOB** odczytuje źródłowy obiekt BLOB archiwum, aby utworzyć nowy obiekt BLOB w trybie online w wybranej warstwie docelowej. Nowy obiekt BLOB może być widoczny podczas wyświetlania listy obiektów blob, ale dane nie są dostępne do momentu zakończenia odczytu ze źródłowego obiektu BLOB archiwum, a dane są zapisywane w nowym docelowym obiekcie blob online. Nowy obiekt BLOB jest niezależną kopią, a jakakolwiek modyfikacja lub usunięcie nie ma wpływu na źródłowy obiekt BLOB archiwum.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Ponownego wypełniania obiekty blob z archiwum do warstwy gorąca lub chłodna są obciążane jako operacje odczytu i pobierania danych. Użycie wysokiego priorytetu (wersja zapoznawcza) obejmuje wyższe koszty operacji i pobierania danych w porównaniu z priorytetem standardowym. Uzupełnianie o wysokim priorytecie jest wyświetlane jako osobny wiersz na rachunku. Jeśli żądanie o wysokim priorytecie zwracające obiekt BLOB archiwum z kilku gigabajtów trwa ponad 5 godzin, nie zostanie naliczona opłata za stawkę za pobieranie o wysokim priorytecie. Jednak standardowe stawki za pobieranie nadal są stosowane, ponieważ oddanie ma priorytet w innych żądaniach.

Kopiowanie obiektów blob z archiwum do warstwy gorąca lub chłodna podlega opłatom za operacje odczytu i pobieranie danych. Dla operacji zapisu jest naliczana opłata za utworzenie nowej kopii obiektu BLOB. Opłaty za wczesne usunięcie nie mają zastosowania podczas kopiowania do obiektu BLOB w trybie online, ponieważ źródłowy obiekt BLOB nie jest modyfikowany w warstwie archiwum. Opłaty za pobieranie o wysokim priorytecie mają zastosowanie w przypadku wybrania tej wartości.

Obiekty blob w warstwie archiwum powinny być przechowywane przez co najmniej 180 dni. Usunięcie lub ponownego wypełniania zarchiwizowanych obiektów BLOB przed 180 dni spowoduje naliczenie opłat za wczesne usunięcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen blokowych obiektów blob i uzupełniania danych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Aby uzyskać więcej informacji na temat opłat za transfer danych wychodzących, zobacz [szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o warstwach Blob Storage](storage-blob-storage-tiers.md)
* [Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)
* [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
* [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
