---
title: Połącz dane Symantec AWS z podglądem platformy Azure — Wersja zapoznawcza | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z danymi firmy Symantec AWS z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 246d4cd7d64554ae575767cdba2e26066ad1720d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295616"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Połącz wskaźnik platformy Azure z usługą AWS CloudTrail

Za pomocą łącznika AWS można przesyłać strumieniowo wszystkie zdarzenia CloudTrail AWS do platformy Azure. Ten proces połączenia deleguje dostęp do dzienników zasobów usługi Azure AWS, tworząc relacje zaufania między AWS CloudTrail i platformą Azure. Jest to realizowane w witrynie AWS, tworząc rolę, która przyznaje uprawnienia do uzyskiwania dostępu do dzienników AWS na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć uprawnienie do zapisu w obszarze roboczym wskaźnik platformy Azure.

> [!NOTE]
> Na platformie Azure wskaźnik zbiera zdarzenia CloudTrail ze wszystkich regionów. Zaleca się, aby nie przesyłać strumieniowo zdarzeń z jednego regionu do innego.

## <a name="connect-aws"></a>Łączenie usługi AWS 


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz wiersz **Amazon Web Services** w tabeli i w okienku AWS z prawej strony kliknij pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami w sekcji **Konfiguracja** , wykonując następujące kroki.
 
1.  W konsoli Amazon Web Services w obszarze **zabezpieczenia, tożsamość & zgodność**, wybierz pozycję **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Wybierz **role** i wybierz pozycję **Utwórz rolę**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Wybierz **inne konto AWS.** W polu **Identyfikator konta** wprowadź **Identyfikator konta Microsoft** (**123412341234**), który można znaleźć na stronie łącznika AWS w portalu Azure.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Upewnij się, że wybrano opcję **Wymagaj identyfikatora zewnętrznego** , a następnie wprowadź identyfikator zewnętrzny (identyfikator obszaru roboczego), który można znaleźć na stronie łącznika AWS w portalu Azure.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  W obszarze **Dołącz uprawnienia zasady** wybierz pozycję **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Wprowadź tag (opcjonalnie).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Następnie wprowadź **nazwę roli** i wybierz przycisk **Utwórz rolę** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na liście role Wybierz utworzoną rolę.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Skopiuj **rolę ARN**. W portalu badania na platformie Azure na ekranie Amazon Web Services łącznika wklej go do roli, **Aby dodać** pole, a następnie kliknij przycisk **Dodaj**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń AWS, Wyszukaj **AWSCloudTrail**.



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia AWS CloudTrail z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

