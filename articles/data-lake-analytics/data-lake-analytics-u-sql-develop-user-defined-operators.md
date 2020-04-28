---
title: Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL — Azure Data Lake Analytics
description: Dowiedz się, jak opracowywać zdefiniowane przez użytkownika operatory do użycia i ponownego użycia w Azure Data Lake Analytics zadaniach.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71316540"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL (udo)
W tym artykule opisano sposób tworzenia operatorów zdefiniowanych przez użytkownika w celu przetwarzania danych w zadaniu U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiowanie i używanie operatora zdefiniowanego przez użytkownika w języku U-SQL
**Aby utworzyć i przesłać zadanie U-SQL**

1. W programie Visual Studio wybierz kolejno pozycje **plik > nowy > projekt > projekt U-SQL**.
2. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie przy użyciu pliku Script. usql.
3. W **Eksplorator rozwiązań**rozwiń węzeł Script. usql, a następnie kliknij dwukrotnie pozycję **Script.usql.cs**.
4. Wklej następujący kod do pliku:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Otwórz **skrypt. usql**i wklej następujący skrypt U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Określ konto usługi Data Lake Analytics, bazę danych i schemat.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt).
9. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
10. Jeśli nie masz połączenia z subskrypcją platformy Azure, zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure.
11. Kliknij przycisk **Prześlij**. Wyniki przesłania i link zadania są dostępne w oknie wyników po zakończeniu przesłania.
12. Kliknij przycisk **Odśwież** , aby wyświetlić najnowszy stan zadania i odświeżyć ekran.

**Aby wyświetlić dane wyjściowe**

1. W **Eksplorator serwera**rozwiń węzeł **Azure**, rozwiń pozycję **Data Lake Analytics**, rozwiń konto Data Lake Analytics, rozwiń węzeł **konta magazynu**, kliknij prawym przyciskiem myszy magazyn domyślny, a następnie kliknij pozycję **Eksplorator**.
2. Rozwiń pozycję przykłady, rozwiń węzeł dane wyjściowe, a następnie kliknij dwukrotnie pozycję **Drivers. csv**.

## <a name="see-also"></a>Zobacz także
* [Rozszerzanie wyrażeń U-SQL z kodem użytkownika](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Używanie narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
