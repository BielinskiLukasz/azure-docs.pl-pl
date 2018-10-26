---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 752c43604349a2361a8f5b26cd6d0bce7b516bc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134209"
---
### <a name="prerequisites"></a>Wymagania wstępne
* A [MailChimp](https://www.MailChimp.com/) konta 

Zanim użyjesz swojego konta usługi MailChimp w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi MailChimp. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi MailChimp:

1. Aby utworzyć połączenie do usługi MailChimp, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *MailChimp* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![Usługi MailChimp w kroku 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Nie utworzono żadnych połączeń do usługi MailChimp, zanim będzie pobrać monit podaj swoje poświadczenia usługi MailChimp. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do Twojego konta usługi MailChimp danych:  
   ![Usługi MailChimp w kroku 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Podaj usługi MailChimp, nazwę użytkownika i hasło, aby autoryzować aplikację logiki:  
   ![Usługi MailChimp w kroku 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Zwróć uwagę, połączenie zostanie utworzone i są teraz bezpłatne wykonaj pozostałe kroki w aplikacji logiki:  
   ![Usługi MailChimp, krok 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

