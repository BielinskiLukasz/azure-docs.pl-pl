---
title: Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie lub Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: b5daf283df1ef5d6b42da5bf0a4652aedf2f6284
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708743"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL (wersja zapoznawcza)

Machine Learning Services to funkcja wystąpienia zarządzanego usługi Azure SQL (wersja zapoznawcza) zapewniająca Uczenie maszynowe w bazie danych, obsługujące skrypty Python i R. Ta funkcja obejmuje pakiety Microsoft Python i języka R dla wysokiej wydajności analizy predykcyjnej i uczenia maszynowego. Dane relacyjne mogą być używane w skryptach za pomocą procedur składowanych, skryptu T-SQL zawierającego instrukcje języka Python lub języka R lub kodu Python lub R zawierającego język T-SQL.

> [!IMPORTANT]
> Usługi Machine Learning Services to funkcja usługi Azure SQL Managed Instance, która jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta funkcja w wersji zapoznawczej jest początkowo dostępna w ograniczonej liczbie regionów w Stanach Zjednoczonych, Europie Azja i Australii z dodatkowymi regionami dodawanymi później.
>
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Utwórz konto w wersji zapoznawczej](#signup) poniżej.

## <a name="what-is-machine-learning-services"></a>Co to jest Machine Learning Services?

Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL umożliwia wykonywanie skryptów Python i języka R w bazie danych. Za jego pomocą można przygotować i oczyścić dane, przeprowadzić Inżynieria funkcji oraz uczenie, oszacować i wdrożyć modele uczenia maszynowego w ramach bazy danych. Funkcja uruchamia skrypty, w których znajdują się dane, i eliminuje transfer danych przez sieć na inny serwer.

Użyj Machine Learning Services z obsługą języka R/Python w wystąpieniu zarządzanym Azure SQL, aby:

- **Uruchamiaj skrypty języka r i Python w celu przygotowania danych i ogólnego przeznaczenia danych** — możesz teraz przenieść skrypty języka R/Python do wystąpienia zarządzanego usługi Azure SQL, w którym znajdują się dane, zamiast przenosić dane na inny serwer w celu uruchamiania skryptów języka r i Python. Można wyeliminować konieczność przenoszenia danych i związanych z nimi problemów związanych z opóźnieniami, bezpieczeństwem i zgodnością.

- **Uczenie modeli uczenia maszynowego w bazie danych** — można nauczyć modele przy użyciu dowolnych algorytmów Open Source. Możesz łatwo skalować swoje szkolenia do całego zestawu danych, a nie polegać na przykładowych zestawach danych, które są pobierane z bazy.

- **Wdrażaj modele i skrypty w środowisku produkcyjnym w procedurach składowanych** — skrypty i modele przeszkolone mogą być inicjowane po prostu przez osadzenie ich w procedurach składowanych T-SQL. Aplikacje łączące się z wystąpieniem zarządzanym usługi Azure SQL mogą korzystać z prognoz i analiz w tych modelach przez wywołanie procedury składowanej. Można również użyć natywnej funkcji przewidywania T-SQL do operacjonalizować modeli szybkiego oceniania w wysoce współbieżnych scenariuszach w czasie rzeczywistym.

Podstawowe dystrybucje języków Python i R są zawarte w Machine Learning Services. Możesz zainstalować i używać pakietów i struktur typu open source, takich jak PyTorch, TensorFlow i scikit — Dowiedz się, oprócz pakietów firmy Microsoft [biblioteki revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) i [Microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) dla języka Python oraz [kolekcję funkcji revoscaler](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [Microsoftml](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [OLAP](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)i [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) w języku R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Tworzenie konta na potrzeby korzystania z wersji zapoznawczej

Ta ograniczona publiczna wersja zapoznawcza podlega [postanowieniom dotyczącym wersji zapoznawczej platformy Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Jeśli interesuje Cię dołączenie do programu w wersji zapoznawczej i akceptujesz te warunki, możesz poprosić o rejestrację, tworząc bilet pomocy technicznej platformy Azure pod adresem [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) . W obszarze "typ problemu" Wybierz "techniczne" dla "usługa" Wybierz "SQL Database wystąpienie zarządzane" i "typ problemu" Wybierz pozycję "inne". Poinformuj nas, że chcesz zarejestrować się w ograniczonej publicznej wersji zapoznawczej usługi Machine Learning dla usługi SQL Managed Instance i podaj następujące informacje: nazwa serwera logicznego, region i identyfikator subskrypcji.

Gdy zarejestrujesz się w programie, firma Microsoft dołączy Cię do publicznej wersji zapoznawczej i włączy usługi Machine Learning Services dla Twojej istniejącej lub nowej bazy danych.

W czasie korzystania z publicznej wersji zapoznawczej nie zaleca się używania usług Machine Learning Services w usłudze SQL Managed Instance na potrzeby obciążeń produkcyjnych.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z kluczowymi różnicami w Machine Learning Services SQL Server](machine-learning-services-differences.md).
- Aby dowiedzieć się, jak używać języka Python w Machine Learning Services, zobacz [Uruchamianie skryptów Python](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Aby dowiedzieć się, jak używać języka R w Machine Learning Services, zobacz [Uruchamianie skryptów języka r](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Aby uzyskać więcej informacji na temat uczenia maszynowego na innych platformach SQL, zobacz [dokumentację usługi SQL Machine Learning](https://docs.microsoft.com/sql/machine-learning/).
