---
title: Współpraca z bazą wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostępna z Access Control opartych na rolach platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: d9c91d54fb357807682cd57f46b04454e4e2cfec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876663"
---
# <a name="collaborate-on-your-knowledge-base"></a>Współpracuj nad bazą wiedzy

QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostępna z [Access Control opartych na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)platformy Azure. 

Wykonaj następujące kroki, aby udostępnić usługę QnA Maker komuś:

1. Zaloguj się do Azure Portal i przejdź do zasobu QnA Maker.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Przejdź do karty **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Wybierz **właściciela** lub rolę **współautor** . Nie można udzielić dostępu tylko do odczytu za pośrednictwem Access Control opartych na rolach. Role Owner i współautor mają uprawnienia dostępu do odczytu i zapisu do usługi QnA Maker.

    ![QnA Maker Dodaj rolę w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Wprowadź adres e-mail użytkownika i naciśnij pozycję **Zapisz**.

    ![QnA Maker Dodaj adres e-mail w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Gdy osoba, do której udostępniono usługę QnA Maker, loguje się do [portalu QNA Maker](https://qnamaker.ai) , mogą zobaczyć wszystkie bazy wiedzy w tej usłudze.

Należy pamiętać, że nie można udostępnić konkretnej bazy wiedzy w usłudze QnA Maker. Jeśli potrzebujesz bardziej szczegółowej kontroli dostępu, rozważ dystrybuowanie baz wiedzy w różnych usługach QnA Maker.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)
