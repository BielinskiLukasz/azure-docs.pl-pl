---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763099"
---
Nie: usunięcie punktu końcowego serwera nie jest takie jak ponowne uruchamianie serwera! Usunięcie i ponowne utworzenie punktu końcowego serwera prawie nigdy nie jest właściwe rozwiązanie, aby Rozwiązywanie problemów z synchronizacji, obsługi warstw w chmurze lub innych aspektów wybranego działania usługi Azure File Sync. Usuwanie punktu końcowego serwera jest operacją destrukcyjną, i może powodować utratę danych w przypadku, że pliki warstwowe znajdują się poza przestrzeni nazw punktu końcowego serwera (zobacz [Dlaczego pliki warstwowe znajdują się poza przestrzeni nazw punktu końcowego serwera](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) dla więcej informacji) lub niedostępności plików do plików warstwowych w ramach przestrzeni nazw punktu końcowego serwera. Te problemy nie zostanie rozwiązany, gdy punkt końcowy serwera są odtwarzane. Pliki warstwowe mogą istnieć w przestrzeni nazw punktu końcowego serwera, nawet wtedy, gdy nigdy nie przeprowadzono obsługi warstw włączone w chmurze. Dlatego zaleca się, że nie spowoduje to usunięcia punktu końcowego serwera, chyba że chcesz zaprzestać korzystania z usługi Azure File Sync za pomocą tego konkretnego folderu lub jawnie zalecił Aby to zrobić, inżynier z firmy Microsoft. Aby uzyskać więcej informacji, Usuń punkty końcowe z serwera, zobacz [usunąć punkt końcowy serwera](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    