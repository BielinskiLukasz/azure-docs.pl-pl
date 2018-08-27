### <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Wunderlist  

Zanim użyjesz swojego konta usługi Wunderlist w aplikacji logiki, należy autoryzować aplikację logiki, aby połączyć się z kontem usługi Wunderlist. Na szczęście możesz można to zrobić z poziomu aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono kroki, aby autoryzować aplikację logiki, aby połączyć się z kontem usługi Wunderlist:

1. Aby utworzyć połączenie do aplikacji Wunderlist, w Projektancie aplikacji logiki, wybierz pozycję **Pokaż Microsoft zarządzane interfejsy API** na liście rozwijanej wprowadź *Wunderlist* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, którą Ci się spodoba do użycia:  
   ![](./media/connectors-create-api-wunderlist/wunderlist-0.png)
2. Nie utworzono żadnych połączeń do aplikacji Wunderlist, zanim będzie pobrać monit podaj swoje poświadczenia aplikacji Wunderlist. Te poświadczenia będą używane do autoryzowania aplikację logiki, aby nawiązać połączenie i dostęp do danych na koncie usługi Wunderlist:   
   ![](./media/connectors-create-api-wunderlist/wunderlist-1.png)  
3. Podaj swoje poświadczenia, a następnie wybierz przycisk logowania  
   ![](./media/connectors-create-api-wunderlist/wunderlist-2.png)  
4. Użytkownik będzie następnie informację, co aplikacja logiki będzie mieć uprawnienia do wykonania na Twoim koncie usługi Wunderlist. Jeśli wyrazisz zgodę, wybierz przycisk aby wskazać umowy. 
   ![](./media/connectors-create-api-wunderlist/wunderlist-4.png)  
5. Na koniec wybierz pozycję **Autoryzuj** przycisku  
   ![](./media/connectors-create-api-wunderlist/wunderlist-5.png)  

