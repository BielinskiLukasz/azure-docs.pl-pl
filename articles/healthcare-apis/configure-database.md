---
title: Konfigurowanie ustawień bazy danych w usłudze Azure API for FHIR
description: W tym artykule opisano sposób konfigurowania ustawień bazy danych w usłudze Azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 652445a96acfa0358211d1d97e0fcf288989d6ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795783"
---
# <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych 

Interfejs API platformy Azure dla usługi FHIR używa bazy danych do przechowywania danych. Wydajność podstawowej bazy danych zależy od liczby jednostek żądań wybranych podczas aprowizacji usług lub w ustawieniach bazy danych po zainicjowaniu obsługi administracyjnej usługi.

Usługa Azure API for FHIR zażyczy koncepcji jednostek ru z Cosmos DB (zobacz [jednostki żądań w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/request-units)) podczas ustawiania wydajności źródłowej bazy danych. 

Obsługa przepływności musi być zapewniona, aby zapewnić, że dla bazy danych są dostępne wystarczające zasoby systemowe. Ile jednostek ru potrzebujesz dla aplikacji, zależy od wykonywanych operacji. Operacje mogą być różne od prostych operacji odczytu i zapisu do bardziej złożonych zapytań. 

> [!NOTE]
> Ponieważ różne operacje zużywają różną liczbę RU, zwracamy rzeczywistą liczbę jednostek ru zużytych w każdym wywołaniu interfejsu API w nagłówku odpowiedzi. W ten sposób można profilować liczbę jednostek ru używanych przez aplikację.

## <a name="update-throughput"></a>Przepustowość aktualizacji

Aby zmienić to ustawienie w Azure Portal, przejdź do interfejsu API platformy Azure dla usługi FHIR i Otwórz blok bazy danych. Następnie zmień zainicjowaną przepływność na żądaną wartość w zależności od potrzeb związanych z wydajnością. Można zmienić wartość maksymalnie 10 000 RU/s. Jeśli potrzebujesz wyższej wartości, skontaktuj się z pomocą techniczną platformy Azure.

Jeśli przepływność bazy danych jest większa niż 10 000 RU/s lub jeśli dane przechowywane w bazie danych przekraczają 50 GB, aplikacja kliencka musi być w stanie obsługiwać tokeny kontynuacji. Nowa partycja zostanie utworzona w bazie danych dla każdego wzrostu przepływności o 10 000 RU/s lub jeśli ilość przechowywanych danych jest większa niż 50 GB. Wiele partycji tworzy wielostronicową odpowiedź, w której wdrożono stronicowanie przy użyciu tokenów kontynuacji.

> [!NOTE] 
> Wyższa wartość oznacza wyższy interfejs API platformy Azure do FHIR przepływności i wyższe koszty usługi.

![Cosmos DB konfiguracji](media/database/database-settings.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób aktualizowania jednostek ru dla interfejsu API platformy Azure dla usługi FHIR. Następnie wdróż w pełni zarządzany interfejs API platformy Azure dla usługi FHIR:
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)
