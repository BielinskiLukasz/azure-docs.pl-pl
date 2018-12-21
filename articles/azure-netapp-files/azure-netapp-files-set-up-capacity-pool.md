---
title: Konfigurowanie puli pojemności na potrzeby usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania puli pojemności, w której można następnie tworzyć woluminy.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412915"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności
Skonfigurowanie puli pojemności umożliwia tworzenie woluminów w tej puli.  

## <a name="before-you-begin"></a>Przed rozpoczęciem 
Potrzebujesz utworzonego konta usługi NetApp.   

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroki 

1. Przejdź do bloku zarządzania na koncie usługi NetApp, a następnie w okienku nawigacji wybierz pozycję **Pule pojemności**.

2. Kliknij pozycję **+ Dodaj pule**, aby dodać nową pulę pojemności.   
    Zostanie wyświetlone okno Nowa pula pojemności.

3. Podaj następujące informacje dotyczące nowej puli pojemności:  
  * **Nazwa**  
    Określ nazwę puli pojemności.  
    Nazwa puli pojemności musi być unikatowa na tym koncie usługi NetApp.

  * **Poziom usługi**   
    To pole wskazuje docelową wydajność puli pojemności.  
    Obecnie jest dostępny tylko poziom usługi Premium. 

  *  **Rozmiar**     
      Określ rozmiar kupowanej puli pojemności.        
      Minimalny rozmiar puli pojemności to 4 TiB. Można utworzyć pulę o rozmiarze będącym wielokrotnością 4 TiB.   
      
      ![Nowa pula pojemności](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki 

[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)


