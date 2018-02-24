1. Skopiuj Instalatora na folder lokalny (na przykład /tmp) na serwerze, który chcesz chronić. W terminalu uruchom następujące polecenia:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Aby zainstalować usługi mobilności, uruchom następujące polecenie:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Po zakończeniu instalacji usługi mobilności musi być zarejestrowany na serwerze konfiguracji. Uruchom następujące polecenie, aby zarejestrować serwer konfiguracji usługi mobilności:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Wiersz polecenia Instalatora usługi mobilności

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parametr|Typ|Opis|Możliwe wartości|
|-|-|-|-|
|-r |Obowiązkowy|Określa, czy należy zainstalować usługi mobilności (MS) lub MasterTarget (MT) powinien być zainstalowany.|MS </br> MT|
|-d |Optional (Opcjonalność)|Lokalizacja, w którym jest zainstalowana usługa mobilności.|/usr/local/ASR|
|-v|Obowiązkowy|Określa platformę, na którym zainstalowano usługę mobilności. </br> </br>- **VMware**: Użyj tej wartości, po zainstalowaniu usługi mobilności na maszynie Wirtualnej systemem *hosty programu VMware vSphere ESXi*, *hosty funkcji Hyper-V*, i *serwerów fizycznych*. </br> - **Azure**: Użyj tej wartości, po zainstalowaniu agenta na maszynie Wirtualnej platformy Azure IaaS.| VMware </br> Azure|
|-q|Optional (Opcjonalność)|Określa, aby uruchomić Instalatora w trybie dyskretnym.| ND|


#### <a name="mobility-service-configuration-command-line"></a>Wiersz polecenia w konfiguracji usługi mobilności

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parametr|Typ|Opis|Możliwe wartości|
|-|-|-|-|
|-i |Obowiązkowy|Adres IP serwera konfiguracji|Dowolny prawidłowy adres IP|
|-P |Obowiązkowy|Pełna ścieżka pliku dla pliku, gdzie hasło połączenia zostanie zapisany|Nieprawidłowa folderu|
