1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W obszarze **Przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.
    ![Przed rozpoczęciem](./media/site-recovery-add-configuration-server/combined-wiz1.png)
3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.

    ![Oprogramowanie innych producentów](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. W obszarze **Rejestracja** wybierz klucz rejestracji pobrany z magazynu.

    ![Rejestracja](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. W obszarze **Ustawienia internetowe** określ, jak dostawca działający na serwerze konfiguracji ma się łączyć z usługą Azure Site Recovery przez Internet.

   * Jeśli chcesz łączyć się z serwerem proxy aktualnie skonfigurowanym na maszynie, wybierz opcję **Połącz przy użyciu istniejących ustawień serwera proxy**.
   * Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio bez serwera proxy**.
   * Jeśli istniejący serwer proxy wymaga uwierzytelniania lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**.

     * Jeśli używasz niestandardowego serwera proxy, musisz określić adres, port i poświadczenia.
     * W przypadku użycia serwera proxy już zezwolono na użycie adresów URL opisanych w [wymaganiach wstępnych](#prerequisites).

     ![Zapora](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli tak, instalator sprawdzi, czy jest zainstalowany program PowerCLI 6.0.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)

9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. W obszarze **Wybór sieci** określ odbiornik (kartę sieciową i port SSL), za pomocą którego serwer konfiguracji będzie wysyłać i odbierać dane replikacji. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania i odbierania ruchu związanego z replikacją.

    ![Wybór sieci](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.

    ![Podsumowanie](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia** > **Serwery** w magazynie.


<!--HONumber=Feb17_HO4-->


