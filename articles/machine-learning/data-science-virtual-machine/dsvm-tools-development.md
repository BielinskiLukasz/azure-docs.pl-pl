---
title: "Narzędzia deweloperskie maszyny wirtualnej nauki danych - Azure | Dokumentacja firmy Microsoft"
description: "Dane nauki wirtualnego narzędzia deweloperskie maszyny."
keywords: "narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 0d7ee9a54c2c11f814ce041c5c7e5bd9f2e605e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Narzędzia deweloperskie na maszynie wirtualnej nauki danych

Maszyna wirtualna nauki danych (DSVM) zapewnia wydajne środowisko do programowania przez grupowanie kilku popularnych narzędzi i IDE. Poniżej przedstawiono niektóre narzędzia, które znajdują się na DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Co to jest?   | Ogólnego przeznaczenia IDE      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Tworzenie oprogramowania    |
| Jak jest on skonfigurowany / zainstalowanym DSVM?      | Obciążenie nauki danych (narzędzia Python i R), Azure obciążenia (na platformie Hadoop, Data Lake), Node.js, narzędzia programu SQL Server [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak używać / uruchom go?      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Pokrewne narzędzia na DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Ogólnego przeznaczenia IDE      |
| DSVM obsługiwane wersje      | Windows, Linux     |
| Typowe zastosowania      | Edytor kodu i integracja z usługą Git   |
| Jak używać / uruchom go?      | Skrót na pulpicie (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) w systemie Windows, skrót na pulpicie lub terminali (`code`) w systemie Linux    |
| Pokrewne narzędzia na DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Programu RStudio pulpitu 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient IDE dla języka R    |
| DSVM obsługiwane wersje      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie R     |
| Jak używać / uruchom go?      | Skrót na pulpicie (`C:\Program Files\RStudio\bin\rstudio.exe`) w systemie Windows, skrót na pulpicie (`/usr/bin/rstudio`) w systemie Linux      |
| Pokrewne narzędzia na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Serwer programu RStudio 
|    |           |
| ------------- | ------------- |
| Co to jest?   | IDE opartych na sieci Web dla języka R    |
| DSVM obsługiwane wersje      | Linux      |
| Typowe zastosowania      |  Programowanie R     |
| Jak używać / uruchom go?      | Włącz usługę z _systemctl włączenia serwera programu rstudio_, następnie uruchom usługę za pomocą _systemctl start serwera programu rstudio_. Użytkownik może następnie zaloguj się do serwera programu RStudio w http://your-vm-ip:8787.       |
| Pokrewne narzędzia na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient IDE języka Julia   |
| DSVM obsługiwane wersje      | Windows, Linux      |
| Typowe zastosowania      |  Programowanie Julia     |
| Jak używać / uruchom go?      | Skrót na pulpicie (`C:\JuliaPro-0.5.1.1\Juno.bat`) w systemie Windows, skrót na pulpicie (`/opt/JuliaPro-VERSION/Juno`) w systemie Linux      |
| Pokrewne narzędzia na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Co to jest?   | Klient IDE języka Python    |
| DSVM obsługiwane wersje      | Linux      |
| Typowe zastosowania      |  Programowanie R     |
| Jak używać / uruchom go?      | Skrót na pulpicie (`/usr/bin/pycharm`) w systemie Linux      |
| Pokrewne narzędzia na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>Usługa Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Co to jest?   | Dane interakcyjne wizualizacje i narzędzia do analizy Biznesowej    |
| DSVM obsługiwane wersje      | Windows  |
| Typowe zastosowania      |  Wizualizacja danych i tworzenie pulpitów nawigacyjnych   |
| Jak używać / uruchom go?      | Skrót na pulpicie (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Pokrewne narzędzia na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

