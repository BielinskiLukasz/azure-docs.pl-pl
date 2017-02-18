Kroki wyrejestrowywania serwera przetwarzania różnią się zależnie od jego stanu połączenia z serwerem konfiguracji.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Połączono

1. Zaloguj się zdalnie do serwera przetwarzania jako administrator.
2. Uruchom **Panel sterowania** i otwórz pozycje **Programy > Odinstaluj program**
3. Odinstaluj program o nazwie **Serwer konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**
4. Po ukończeniu kroku 3 możesz odinstalować **zależności serwera konfiguracji/przetwarzania usługi Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Wyrejestrowywanie serwera przetwarzania, który jest w stanie Rozłączono

> [!WARNING]
> Poniższych kroków należy użyć, jeśli nie ma sposobu reaktywowania maszyny wirtualnej, na której zainstalowano serwer przetwarzania.

1. Zaloguj się do serwera konfiguracji jako administrator.
2. Otwórz administracyjny wiersz polecenia i przejdź do katalogu `%ProgramData%\ASR\home\svsystems\bin`.
3. Teraz uruchom polecenie.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Spowoduje to wyczyszczenie informacji o serwerze przetwarzania z systemu.


<!--HONumber=Feb17_HO1-->


