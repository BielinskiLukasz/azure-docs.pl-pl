---
title: Rozwiązywanie problemów z obszarem roboczym
titleSuffix: Azure Machine Learning Studio (classic)
description: Ten przewodnik zawiera rozwiązania niektórych często spotykanych problemów podczas konfigurowania klasycznej wersji Azure Machine Learning Studio obszarów roboczych.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7d4aebf71ef16287a415e4c39eed1328c9359f6c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492591"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Przewodnik rozwiązywania problemów: Tworzenie obszaru roboczego Azure Machine Learning Studio (klasyczny) i nawiązywanie z nim połączenia
Ten przewodnik zawiera rozwiązania niektórych często spotykanych problemów podczas konfigurowania obszarów roboczych Azure Machine Learning Studio (klasycznych).



## <a name="workspace-owner"></a>Właściciel obszaru roboczego
Aby otworzyć obszar roboczy w Machine Learning Studio (klasyczny), użytkownik musi być zalogowany na koncie Microsoft użytym do utworzenia obszaru roboczego lub uzyskać zaproszenia od właściciela, aby dołączyć do obszaru roboczego. Z Azure Portal można zarządzać obszarem roboczym, który obejmuje możliwość konfigurowania dostępu.

Aby uzyskać więcej informacji na temat zarządzania obszarem roboczym, zobacz [Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznym)].

[Zarządzanie obszarem roboczym Azure Machine Learning Studio (klasycznym)]: manage-workspace.md

## <a name="allowed-regions"></a>Dozwolone regiony
Machine Learning jest obecnie dostępna w ograniczonej liczbie regionów. Jeśli subskrypcja nie obejmuje jednego z tych regionów, może zostać wyświetlony komunikat o błędzie "nie masz żadnych subskrypcji w dozwolonych regionach".

Aby zażądać dodania regionu do subskrypcji, Utwórz nowe żądanie pomocy technicznej firmy Microsoft z Azure Portal, wybierz **rozliczenia** jako typ problemu i postępuj zgodnie z monitami, aby przesłać żądanie.

## <a name="storage-account"></a>Konto magazynu
Usługa Machine Learning wymaga konta magazynu do przechowywania danych. Możesz użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu podczas tworzenia nowego obszaru roboczego Machine Learning Studio (klasycznego) (Jeśli masz przydział do utworzenia nowego konta magazynu).

Po utworzeniu nowego obszaru roboczego Machine Learning Studio (klasyczny) możesz zalogować się do Machine Learning Studio (klasyczny) przy użyciu konto Microsoft użytego do utworzenia obszaru roboczego. Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono obszaru roboczego" (podobnego do poniższego zrzutu ekranu), wykonaj poniższe kroki, aby usunąć pliki cookie przeglądarki.

![Nie znaleziono obszaru roboczego](media/troubleshooting-creating-ml-workspace/screen3.png)

**Aby usunąć pliki cookie przeglądarki**

1. Jeśli używasz programu Internet Explorer, kliknij przycisk **Narzędzia** w prawym górnym rogu i wybierz pozycję **Opcje internetowe**.  

   ![Opcje internetowe](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na karcie **Ogólne** kliknij przycisk **Usuń.**

   ![Karta Ogólne](media/troubleshooting-creating-ml-workspace/screen5.png)

3. W oknie dialogowym **usuwanie historii przeglądania** upewnij się, że **pliki cookie i dane witryny sieci Web** są zaznaczone, a następnie kliknij pozycję **Usuń**.

   ![Usuń pliki cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po usunięciu plików cookie ponownie uruchom przeglądarkę, a następnie przejdź do strony [Microsoft Azure Machine Learning Studio (klasyczne)](https://studio.azureml.net) . Gdy zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź tę samą konto Microsoft, która została użyta do utworzenia obszaru roboczego.

## <a name="comments"></a>Komentarze

Naszym celem jest zapewnienie bezproblemowego działania Machine Learning. Zamieszczaj wszelkie komentarze i problemy na [forum Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) , aby pomóc nam w lepszym ulepszaniu.
