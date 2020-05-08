---
title: Zabezpieczanie obszaru roboczego Synapse (wersja zapoznawcza)
description: W tym artykule opisano sposób korzystania z ról i kontroli dostępu w celu kontrolowania działań i uzyskiwania dostępu do danych w obszarze roboczym Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 383a28e90c093c0885c34af03371608261ece110
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692630"
---
# <a name="secure-your-synapse-workspace-preview"></a>Zabezpieczanie obszaru roboczego Synapse (wersja zapoznawcza)

W tym artykule opisano sposób korzystania z ról i kontroli dostępu w celu kontrolowania działań i uzyskiwania dostępu do danych. Zgodnie z tymi instrukcjami kontrola dostępu w usłudze Azure Synapse Analytics jest uproszczona. Wystarczy dodać i usunąć użytkowników z jednej z trzech grup zabezpieczeń.

## <a name="overview"></a>Omówienie

Aby zabezpieczyć obszar roboczy Synapse (wersja zapoznawcza), postępuj zgodnie ze wzorcem konfigurowania następujących elementów:

- Role platformy Azure (takie jak wbudowane, takie jak właściciel, współautor itp.)
- Role Synapse — te role są unikatowe dla Synapse i nie są oparte na rolach platformy Azure. Istnieją trzy z tych ról:
  - Administrator obszaru roboczego Synapse
  - Synapse administratora SQL
  - Administrator platformy Spark Synapse
- Kontrola dostępu do danych w Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Kontrola dostępu do Synapse baz danych SQL i Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Procedura zabezpieczania obszaru roboczego Synapse

Ten dokument używa standardowych nazw do uproszczenia instrukcji. Zastąp je wszystkimi wybranymi nazwami.

|Ustawienie | Przykładowa wartość | Opis |
| :------ | :-------------- | :---------- |
| **Obszar roboczy Synapse** | WS1 |  Nazwa, która będzie miała obszar roboczy Synapse. |
| **Konto ADLSGEN2** | STG1 | Konto ADLS, które ma być używane z Twoim obszarem roboczym. |
| **Kontener** | CNT1 | Kontener w STG1, który będzie używany domyślnie przez obszar roboczy. |
| **Dzierżawa usługi Active Directory** | contoso | Nazwa dzierżawy usługi Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>Krok 1. Konfigurowanie grup zabezpieczeń

Tworzenie i wypełnianie trzech grup zabezpieczeń dla obszaru roboczego:

- **WS1\_WSAdmins** — dla użytkowników, którzy potrzebują pełnej kontroli nad obszarem roboczym
- **WS1\_SparkAdmins** — dla tych użytkowników, którzy potrzebują pełnej kontroli nad aspektami platformy Spark w obszarze roboczym
- **WS1\_sqladmins** — dla użytkowników, którzy potrzebują pełnej kontroli nad aspektami programu SQL obszaru roboczego
- Dodaj **WS1\_WSAdmins** do **WS1\_sqladmins**
- Dodaj **WS1\_WSAdmins** do **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>Krok 2. Przygotowywanie konta Data Lake Storage Gen2

Zidentyfikuj te informacje o magazynie:

- Konto ADLSGEN2 do użycia w Twoim obszarze roboczym. Ten dokument wywołuje STG1 IT.  STG1 jest uznawana za "podstawowe" konto magazynu dla Twojego obszaru roboczego.
- Kontener w WS1, który będzie używany przez obszar roboczy Synapse domyślnie. Ten dokument wywołuje CNT1 IT.  Ten kontener jest używany na potrzeby:
  - Przechowywanie plików danych kopii zapasowej dla tabel platformy Spark
  - Dzienniki wykonywania dla zadań platformy Spark

- Przy użyciu Azure Portal Przypisz grupy zabezpieczeń następujące role w CNT1

  - Przypisywanie **WS1\_WSAdmins** do roli **współautor danych obiektów blob magazynu**
  - Przypisywanie **WS1\_SparkAdmins** do roli **współautor danych obiektów blob magazynu**
  - Przypisywanie **WS1\_sqladmins** do roli **współautor danych obiektów blob magazynu**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>Krok 3. Tworzenie i Konfigurowanie obszaru roboczego Synapse

W Azure Portal Utwórz obszar roboczy Synapse:

- Nazwij obszar roboczy WS1
- Wybierz STG1 dla konta magazynu
- Wybierz pozycję CNT1 dla kontenera, który jest używany jako "system plików".
- Otwórz WS1 w programie Synapse Studio
- Wybierz pozycję **Zarządzaj** > **Access Control** Przypisz grupy zabezpieczeń do następujących ról Synapse.
  - Przypisywanie **WS1\_WSAdmins** do administratorów obszaru roboczego Synapse
  - Przypisywanie **WS1\_SparkAdmins** do administratorów platformy Synapse Spark
  - Przypisywanie **WS1\_sqladmins** do Synapse Administratorzy SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>Krok 4. Konfigurowanie Data Lake Storage Gen2 do użycia przez obszar roboczy Synapse

Obszar roboczy Synapse wymaga dostępu do STG1 i CNT1, aby można było uruchamiać potoki i wykonywać zadania systemowe.

- Otwórz Azure Portal
- Znajdź STG1
- Przejdź do CNT1
- Upewnij się, że plik MSI (tożsamość usługi zarządzanej) dla WS1 jest przypisany do roli **współautor danych obiektów blob platformy Azure** w witrynie CNT1
  - Jeśli nie widzisz jej przypisanej, przypisz ją.
  - Plik MSI ma taką samą nazwę jak obszar roboczy. W takim przypadku &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>Krok 5. Konfigurowanie dostępu administratora dla pul SQL

- Otwórz Azure Portal
- Przejdź do WS1
- W obszarze **Ustawienia**kliknij pozycję **SQL Active Directory administrator**
- Kliknij pozycję **Ustaw administratora** i wybierz\_pozycję WS1 sqladmins

## <a name="step-6-maintaining-access-control"></a>Krok 6. utrzymywanie kontroli dostępu

Konfiguracja została zakończona.

Teraz, aby zarządzać dostępem użytkowników, można dodawać i usuwać użytkowników z trzech grup zabezpieczeń.

Mimo że można ręcznie przypisać użytkowników do ról Synapse, jeśli tak się stanie, nie będzie on spójnie skonfigurowany. Zamiast tego należy dodawać i usuwać tylko użytkowników z grup zabezpieczeń.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>Krok 7. Weryfikowanie dostępu dla użytkowników w rolach

Użytkownicy w każdej roli muszą wykonać następujące czynności:

|   | Krok | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- | --- |
| 1 | Przekaż plik Parquet do CNT1 | TAK | TAK | TAK |
| 2 | Przeczytaj plik Parquet za pomocą SQL na żądanie | TAK | NO | TAK |
| 3 | Tworzenie puli platformy Spark | TAK [1] | TAK [1] | NO  |
| 4 | Odczytuje plik Parquet z notesem | TAK | TAK | NO |
| 5 | Tworzenie potoku z poziomu notesu i wyzwalanie potoku w celu uruchomienia go teraz | TAK | NO | NO |
| 6 | Utwórz pulę SQL i uruchom skrypt SQL, taki jak &quot;SELECT 1&quot; | TAK [1] | NO | TAK [1] |

> [!NOTE]
> [1] aby utworzyć pule SQL lub Spark, użytkownik musi mieć co najmniej rolę współautor w obszarze roboczym Synapse.
> [!TIP]
>
> - Niektóre kroki zostaną celowo zabronione w zależności od roli.
> - Należy pamiętać, że niektóre zadania mogą się nie powieść, jeśli zabezpieczenia nie zostały w pełni skonfigurowane. Te zadania są wymienione w tabeli.

## <a name="step-8-network-security"></a>Krok 8. zabezpieczenia sieci

W celu skonfigurowania zapory obszaru roboczego, sieci wirtualnej i [linku prywatnego](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>Krok 9. uzupełnianie

Obszar roboczy jest teraz w pełni skonfigurowany i zabezpieczony.

## <a name="how-roles-interact-with-synapse-studio"></a>Jak role współdziałają z programem Synapse Studio

Program Synapse Studio będzie zachowywać się inaczej w zależności od ról użytkownika. Niektóre elementy mogą być ukryte lub wyłączone, jeśli użytkownik nie jest przypisany do ról, które zapewniają odpowiedni dostęp. Poniższa tabela zawiera podsumowanie wpływu programu Synapse Studio.

| Zadanie | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- |
| Otwórz Synapse Studio | TAK | TAK | TAK |
| Wyświetlanie centrum macierzystego | TAK | TAK | TAK |
| Wyświetlanie centrum danych | TAK | TAK | TAK |
| Centrum danych/zobacz połączone konta ADLSGen2 i kontenery | TAK [1] | TAK [1] | TAK [1] |
| Centrum danych/Wyświetlanie baz danych | TAK | TAK | TAK |
| Centrum danych/wyświetlanie obiektów w bazach danych | TAK | TAK | TAK |
| Centra danych/dostęp do danych w puli baz danych SQL | TAK   | NO   | TAK   |
| Centra danych/dostęp do danych w bazach danych SQL na żądanie | TAK [2]  | NO  | TAK [2]  |
| Centra danych/dostęp do danych w bazach danych Spark | TAK [2] | TAK [2] | TAK [2] |
| Korzystanie z centrum opracowywania | TAK | TAK | TAK |
| Opracowywanie skryptów SQL Hub/autora | TAK | NO | TAK |
| Opracowywanie definicji zadań centrum/autora platformy Spark | TAK | TAK | NO |
| Opracowywanie notesów centrum/autora | TAK | TAK | NO |
| Opracowywanie centrów/autora przepływu | TAK | NO | NO |
| Korzystanie z centrum aranżacji | TAK | TAK | TAK |
| Organizuj centra/Użyj potoków | TAK | NO | NO |
| Korzystanie z centrum zarządzania | TAK | TAK | TAK |
| Zarządzanie pulami Hub/SQL | TAK | NO | TAK |
| Zarządzanie pulami Hub/Spark | TAK | TAK | NO |
| Zarządzaj centrum/wyzwalaczami | TAK | NO | NO |
| Zarządzanie centrami/połączonymi usługami | TAK | TAK | TAK |
| Zarządzanie centrami/Access Control (przypisywanie użytkowników do ról obszaru roboczego Synapse) | TAK | NO | NO |
| Zarządzanie centrami/środowiskami Integration Runtime | TAK | TAK | TAK |
| Korzystanie z centrum monitora | TAK | TAK | TAK |
| Monitoruj uruchomienia centrów/aranżacji/potoku  | TAK | NO | NO |
| Monitorowanie uruchomień centrum/aranżacji/wyzwalacza  | TAK | NO | NO |
| Monitorowanie centrów/aranżacji/środowisk Integration Runtime  | TAK | TAK | TAK |
| Monitoruj centra/działania/aplikacje platformy Spark | TAK | TAK | NO  |
| Monitoruj centra/działania/żądania SQL | TAK | NO | TAK |
| Monitoruj centrum/działania/pule platformy Spark | TAK | TAK | NO  |
| Monitoruj centrum/wyzwalacze | TAK | NO | NO |
| Zarządzanie centrami/połączonymi usługami | TAK | TAK | TAK |
| Zarządzanie centrami/Access Control (przypisywanie użytkowników do ról obszaru roboczego Synapse) | TAK | NO | NO |
| Zarządzanie centrami/środowiskami Integration Runtime | TAK | TAK | TAK |


> [!NOTE]
> [1] dostęp do danych w kontenerach zależy od kontroli dostępu w ADLSGen2. </br>
> [2] tabele SQL OD i tabele platformy Spark przechowują swoje dane w ADLSGen2, a dostęp wymaga odpowiednich uprawnień do ADLSGen2.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego Synapse](../quickstart-create-workspace.md)
