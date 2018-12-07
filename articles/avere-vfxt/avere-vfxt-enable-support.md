---
title: Włącz obsługę Avere vFXT - Azure
description: Jak włączyć obsługę operacji przekazywania z Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: b975f84106507da6adff11dc62441526773f5cab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998120"
---
# <a name="enable-support-uploads"></a>Włączanie przekazywania pomocy technicznej

VFXT Avere dla platformy Azure automatycznie przekazać dane o pomocy technicznej dotyczące klastra. Przekazywanie, te umożliwiają personelu obsługi zapewnianie najlepszych usług możliwych klienta.

## <a name="steps-to-enable-uploads"></a>Czynności, aby umożliwić przekazywanie

Wykonaj poniższe kroki z poziomu Panelu sterowania Avere, aby aktywować pomocy technicznej. (Odczytu [dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) informacje na temat otworzyć Panel sterowania Avere.)

1. Przejdź do **ustawienia** kartę u góry.
1. Kliknij przycisk **pomocy technicznej** link po lewej stronie i zaakceptuj zasady zachowania poufności informacji.

   ![Zrzut ekranu przedstawiający Avere Panelu sterowania i wyskakujące okienko ze przycisk Potwierdź, aby zaakceptować zasady zachowania poufności informacji](media/avere-vfxt-privacy-policy.png)

1. Kliknij przycisk trójkąta z lewej strony **informacje o kliencie** aby rozwinąć sekcję.
1. Kliknij przycisk **Revalidate przekazywania informacji** przycisku.
1. Ustaw nazwa_klastra pomocy technicznej w **unikatową nazwę klastra** — upewnij się, unikatowo identyfikuje klaster do personelu obsługi.
1. Zaznacz pola wyboru dla **monitorowania statystyk**, **przekazać ogólne informacje o**, i **przekazywanie informacji o awariach**.
1. Kliknij przycisk **przesłać**.

   ![Zrzut ekranu zawierającego klienta ukończone sekcji informacji o pomocy technicznej, strony ustawień](media/avere-vfxt-support-info.png)

1. Kliknij przycisk trójkąta z lewej strony **Secure aktywne pomocy technicznej (SPS)** aby rozwinąć sekcję.
1. Pole wyboru dla **Włącz łącze dodatki Service Pack**.
1. Kliknij przycisk **przesłać**.

   ![Zrzut ekranu, zawierający ukończone sekcję Secure aktywne pomocy technicznej na stronie Ustawienia pomocy technicznej](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Kolejne kroki

Jeśli trzeba dodać do systemu magazynu lokalnego do klastra lub zaktualizować domyślnego klucza szyfrowania w nowo utworzony kontener obiektów Blob, postępuj zgodnie z instrukcjami [skonfigurować magazyn](avere-vfxt-add-storage.md). 

Jeśli wszystko jest gotowe rozpocząć dołączanie klientów do klastra, przeczytaj [instalacji klastra vFXT Avere](avere-vfxt-mount-clients.md).