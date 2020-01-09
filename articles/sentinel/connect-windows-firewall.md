---
title: Łączenie danych zapory systemu Windows z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane zapory systemu Windows z wskaźnikiem kontrolnym platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: feb7fa0b3f820c3f918fc8a5c817e7fe5fb15fc9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610407"
---
# <a name="connect-windows-firewall"></a>Łączenie z zaporą systemu Windows



Łącznik zapory systemu Windows umożliwia łatwe łączenie dzienników zapór systemu Windows, jeśli są one połączone z obszarem roboczym wskaźnikowego platformy Azure. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Rozwiązanie zbiera zdarzenia Zapory systemu Windows z maszyn z systemem Windows, na których zainstalowano agenta Log Analytics. 


> [!NOTE]
> - Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.
> - Jeśli dane dotyczące platformy Azure i Azure Security Center są zbierane w tym samym obszarze roboczym, nie ma potrzeby włączania rozwiązania Zapora systemu Windows za pomocą tego łącznika. Jeśli ta funkcja została włączona, nie spowoduje to zduplikowanych danych. 

## <a name="enable-the-connector"></a>Włącz łącznik 

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Zapora systemu Windows** . 
1.  Jeśli maszyny z systemem Windows znajdują się na platformie Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynie wirtualnej platformy Azure systemu Windows**.
    1. Na liście **maszyny wirtualne** wybierz maszynę z systemem Windows, która ma zostać przesłana do usługi Azure wskaźnikowej. Upewnij się, że jest to maszyna wirtualna z systemem Windows.
    1. W oknie otwartym dla tej maszyny wirtualnej kliknij pozycję **Połącz**.  
    1. W oknie **łącznika zapory systemu Windows** kliknij pozycję **Włącz** . 

2. Jeśli maszyna z systemem Windows nie jest maszyną wirtualną platformy Azure:
    1. Kliknij przycisk **Zainstaluj agenta na maszynach spoza platformy Azure**.
    1. W oknie **agenta bezpośredniego** wybierz pozycję **Pobierz agenta systemu Windows (64 bit)** lub **pobierz agenta systemu Windows (32 bit)** .
    1. Zainstaluj agenta na komputerze z systemem Windows. Skopiuj **Identyfikator obszaru roboczego**, **klucz podstawowy**i **klucz pomocniczy** , a następnie użyj ich po wyświetleniu monitu podczas instalacji.

4. Wybierz typy danych, które chcesz przesłać strumieniowo.
5. Kliknij przycisk **Zainstaluj rozwiązanie**.
6. Aby użyć odpowiedniego schematu w Log Analytics dla zapory systemu Windows, wyszukaj ciąg " **SecurityEvent**".

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zapory systemu Windows z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

