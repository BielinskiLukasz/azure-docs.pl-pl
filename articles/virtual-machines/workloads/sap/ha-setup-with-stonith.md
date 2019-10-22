---
title: Wysoka dostępność skonfigurowana za pomocą STONITH dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Ustanów wysoką dostępność dla SAP HANA na platformie Azure (duże wystąpienia) w SUSE przy użyciu STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f23fe2aa17934b967e7aecf41687cc555b9552c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71212528"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Wysoka dostępność skonfigurowana w SUSE przy użyciu STONITH
Ten dokument zawiera szczegółowe instrukcje krok po kroku dotyczące konfigurowania wysokiej dostępności systemu operacyjnego SUSE przy użyciu urządzenia STONITH.

**Zastrzeżenie:** *ten przewodnik jest uzyskiwany przez przetestowanie konfiguracji w środowisku dużych wystąpień Microsoft Hana, które pomyślnie zadziałało. W przypadku dużych wystąpień usługi Microsoft Service Management Team for HANA nie jest obsługiwany system operacyjny, jednak może być konieczne skontaktowanie się z firmą SUSE w celu uzyskania dalszych problemów lub wyjaśnień dotyczących warstwy systemu operacyjnego. Zespół zarządzający usługą firmy Microsoft konfiguruje urządzenie STONITH i w pełni obsługuje i może być związane z rozwiązywaniem problemów dotyczących urządzeń STONITH.*
## <a name="overview"></a>Przegląd
Aby skonfigurować wysoką dostępność przy użyciu klastrowania SUSE, należy spełnić następujące wymagania wstępne.
### <a name="pre-requisites"></a>Wymagania wstępne
- Obsługiwane są duże wystąpienia HANA
- System operacyjny jest zarejestrowany
- Serwery z dużymi wystąpieniami HANA są połączone z serwerem SMT w celu pobrania poprawek/pakietów
- W systemie operacyjnym zainstalowano najnowsze poprawki
- Serwer NTP (czas) jest skonfigurowany
- Przeczytaj i zapoznaj się z najnowszą wersją dokumentacji SUSE dotyczącej konfiguracji HA

### <a name="setup-details"></a>Szczegóły konfiguracji
W tym przewodniku zastosowano następującą konfigurację:
- System operacyjny: SLES 12 SP1 dla oprogramowania SAP
- Duże wystąpienia HANA: 2xS192 (cztery gniazda, 2 TB)
- Wersja platformy HANA: HANA 2,0 SP1
- Nazwy serwerów: sapprdhdb95 (Węzeł1) i sapprdhdb96 (Węzeł2)
- Urządzenie STONITH: urządzenie STONITH oparte na technologii iSCSI
- NTP skonfigurowany na jednym z węzłów dużego wystąpienia HANA

Po skonfigurowaniu dużych wystąpień platformy HANA przy użyciu usługi HSR można zażądać od zespołu zarządzania usługami firmy Microsoft, aby skonfigurował STONITH. Jeśli już jesteś istniejącym klientem, który ma wdrożone duże wystąpienia HANA, i potrzebujesz STONITH urządzenia dla istniejących bloków, musisz dostarczyć następujące informacje do zespołu zarządzania usługami firmy Microsoft w formularzu żądania obsługi (SRF). Możesz zażądać formularza SRF za pomocą Menedżera kont technicznych lub kontaktu z firmą Microsoft w przypadku wystąpienia HANA. Nowi klienci mogą żądać STONITH urządzenia w chwili aprowizacji. Dane wejściowe są dostępne w formularzu żądania aprowizacji.

- Nazwa serwera i adres IP serwera (na przykład myhanaserver1, 10.35.0.1)
- Lokalizacja (na przykład Wschodnie stany USA)
- Nazwa klienta (na przykład Microsoft)
- Identyfikator systemu SID-HANA (na przykład H11)

Po skonfigurowaniu urządzenia STONITH zespół zarządzający usługą firmy Microsoft udostępnia nazwę urządzenia SBD i adres IP magazynu iSCSI, za pomocą którego można skonfigurować konfigurację STONITH. 

Aby skonfigurować kompleksową wysoką HA przy użyciu STONITH, należy wykonać następujące czynności:

1.  Identyfikowanie urządzenia SBD
2.  Inicjowanie urządzenia SBD
3.  Konfigurowanie klastra
4.  Konfigurowanie licznika Softdog
5.  Przyłącz węzeł do klastra
6.  Weryfikowanie klastra
7.  Konfigurowanie zasobów w klastrze
8.  Testowanie procesu przełączania do trybu failover

## <a name="1---identify-the-sbd-device"></a>1. Identyfikowanie urządzenia SBD
W tej sekcji opisano sposób określania urządzenia SBD na potrzeby instalacji po skonfigurowaniu STONITH przez zespół zarządzania usługami firmy Microsoft. **Ta sekcja dotyczy tylko istniejącego klienta**. Jeśli jesteś nowym klientem, zespół usługi Microsoft Service Management udostępnia nazwę urządzenia SBD i możesz pominąć tę sekcję.

1,1 Modyfikuj */etc/iSCSI/initiatorname.ISCI* 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Usługa Microsoft Service Management udostępnia ten ciąg. Zmodyfikuj plik w **obu** węzłach, ale numer węzła różni się w każdym węźle.

![Inicjator. png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 Modify */etc/iSCSI/iscsid.conf*: Ustaw *Node. Session. Timeo. replacement_timeout = 5* i *Node. Startup = Automatic*. Zmodyfikuj plik w **obu** węzłach.

1,3 wykonanie polecenia odnajdywania pokazuje cztery sesje. Uruchom ją na obu węzłach.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery. png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 wykonanie polecenia, aby zalogować się do urządzenia iSCSI, pokazuje cztery sesje. Uruchom ją na **obu** węzłach.

```
iscsiadm -m node -l
```
![iSCSIadmLogin. png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 wykonanie skryptu ponownego skanowania: *rescan-SCSI-Bus.sh*.  Ten skrypt przedstawia nowe utworzone dyski.  Uruchom ją na obu węzłach. Powinna zostać wyświetlona liczba numerów LUN większa od zera (na przykład: 1, 2 itd.)

```
rescan-scsi-bus.sh
```
![rescanscsibus. png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 Aby uzyskać nazwę urządzenia, uruchom polecenie *fdisk – l*. Uruchom ją na obu węzłach. Wybierz urządzenie o rozmiarze **178 MIB**.

```
  fdisk –l
```

![fdisk-l. png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. zainicjuj urządzenie SBD

2,1 zainicjuj urządzenie SBD na **obu** węzłach

```
sbd -d <SBD Device Name> create
```
![sbdcreate. png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Sprawdź, co zostało zapisaną na urządzeniu. Zrób to na **obu** węzłach

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Konfigurowanie klastra
W tej sekcji opisano kroki konfigurowania klastra z systemem SUSE HA.
### <a name="31-package-installation"></a>Instalacja pakietu 3,1
3.1.1 Upewnij się, że zainstalowano wzorce ha_sles i SAPHanaSR-doc. Jeśli nie jest zainstalowana, zainstaluj je. Zainstaluj ją na **obu** węzłach.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles. png ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![zypperpatternSAPHANASR-doc. png ](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 konfigurowania klastra
3.2.1 można użyć polecenia *"ha-Cluster-init* " lub użyć Kreatora YaST2, aby skonfigurować klaster. W takim przypadku używany jest Kreator YaST2. Ten krok jest wykonywany **tylko w węźle podstawowym**.

Obserwuj YaST2 > wysoką dostępność > Cluster ![yast-Control-Center. png ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![yast-Hawk-install. png ](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Kliknij przycisk **Anuluj** , ponieważ pakiet halk2 jest już zainstalowany.

![YaST-Hawk-Continue. png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Kliknij przycisk **Kontynuuj** .

Oczekiwana wartość = Liczba wdrożonych węzłów (w tym przypadku 2) ![yast-Cluster-Security. png ](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) kliknij przycisk **dalej** 
 ![yast-Cluster-Configure-csync2. png ](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Dodaj nazwy węzłów, a następnie kliknij pozycję "Dodaj sugerowane pliki".

Kliknij pozycję "Włącz csync2 na"

Kliknij pozycję "Generuj klucze wstępne", co spowoduje wyświetlenie poniższego okna podręcznego

![YaST-Key-File. png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Kliknij przycisk **OK**.

Uwierzytelnianie odbywa się przy użyciu adresów IP i kluczy wstępnych udostępnianych w programie Csync2. Plik klucza jest generowany z csync2-k/etc/csync2/key_hagroup. Plik key_hagroup powinien być kopiowany do wszystkich członków klastra ręcznie po jego utworzeniu. **Upewnij się, że plik jest kopiowany z węzła 1 do Węzeł2**.

![YaST-Cluster-conntrackd. png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Kliknij przycisk **dalej** 
 ![yast-Cluster-Service. png ](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

W przypadku opcji domyślnej rozruch jest wyłączony, zmień go na "on", więc Pacemaker jest uruchamiany przy rozruchu. Wybór można wybrać na podstawie wymagań dotyczących instalacji.
Kliknij przycisk **dalej** , a konfiguracja klastra została ukończona.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Konfigurowanie licznika Softdog
W tej sekcji opisano konfigurację licznika alarmowego (softdog).

4,1 Dodaj następujący wiersz do */etc/init.d/Boot.Local* na **obu** węzłach.
```
modprobe softdog
```
![modprobe-softdog. png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 Zaktualizuj plik */etc/sysconfig/SBD* na **obu** węzłach w następujący sposób:
```
SBD_DEVICE="<SBD Device Name>"
```
![SBD-Device. png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 Załaduj moduł jądra na **obu** węzłach, uruchamiając następujące polecenie
```
modprobe softdog
```
![modprobe-softdog-Command. png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Sprawdź i upewnij się, że softdog działa jak na **obu** węzłach:
```
lsmod | grep dog
```
![lsmod-grep-Dog. png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 uruchom urządzenie SBD na **obu** węzłach
```
/usr/share/sbd/sbd.sh start
```
![SBD-SH-Start. png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 Przetestuj demona SBD na **obu** węzłach. Po skonfigurowaniu **obu** węzłów są wyświetlane dwa wpisy
```
sbd -d <SBD Device Name> list
```
![SBD-list. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7. Wyślij wiadomość testową do **jednego** z węzłów
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD-list. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 w **drugim** węźle (Węzeł2) można sprawdzić stan komunikatu
```
sbd  -d <SBD Device Name> list
```
![SBD-list-Message. png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 aby przyjąć konfigurację SBD, zaktualizuj plik */etc/sysconfig/SBD* w następujący sposób. Aktualizuj plik na **obu** węzłach
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 uruchomienie usługi Pacemaker w **węźle podstawowym** (Węzeł1)
```
systemctl start pacemaker
```
![Start-Pacemaker. png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Jeśli usługa Pacemaker *nie powiedzie*się, zapoznaj się z *scenariuszem 5: usługa Pacemaker kończy się niepowodzeniem*

## <a name="5---joining-the-cluster"></a>5. przyłączanie do klastra
W tej sekcji opisano sposób przyłączania węzła do klastra.

### <a name="51-add-the-node"></a>5,1 Dodaj węzeł
Uruchom następujące polecenie w systemie **Węzeł2** , aby umożliwić Węzeł2 dołączenie do klastra.
```
ha-cluster-join
```
Jeśli *wystąpi błąd* podczas dołączania klastra, zapoznaj się *ze scenariuszem 6: węzeł 2 nie można dołączyć do klastra*.

## <a name="6---validating-the-cluster"></a>6. Sprawdzanie poprawności klastra

### <a name="61-start-the-cluster-service"></a>6,1. Uruchom usługę klastrowania
Aby sprawdzić i opcjonalnie uruchomić klaster po raz pierwszy w **obu** węzłach.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-Pacemaker. png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 monitorowanie stanu
Uruchom polecenie *crm_mon* , aby upewnić się, że **oba** węzły są w trybie online. Można uruchomić ją na **dowolnym węźle** klastra
```
crm_mon
```
![crm-Mon. png ](media/HowToHLI/HASetupWithStonith/crm-mon.png) można także zalogować się do Hawk, aby sprawdzić stan klastra *https://\<node IP >: 7630*. Domyślny użytkownik to hacluster, a hasło to Linux. W razie konieczności można zmienić hasło za pomocą polecenia *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurowanie właściwości i zasobów klastra 
W tej sekcji opisano kroki konfigurowania zasobów klastra.
W tym przykładzie skonfigurujesz następujący zasób, można skonfigurować resztę (w razie konieczności), odwołując się do przewodnika SUSE HA. Wykonaj konfigurację tylko w **jednym z węzłów** . Wykonaj w węźle podstawowym.

- Ładowania początkowego klastra
- Urządzenie STONITH
- Wirtualny adres IP


### <a name="71-cluster-bootstrap-and-more"></a>ładowania początkowego klastra 7,1 i innych
Dodaj ładowania początkowego klastra. Utwórz plik i Dodaj tekst w następujący sposób:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-bs.txt
```
![CRM-Configure-crmbs. png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 urządzenie STONITH
Dodaj STONITH zasobów. Utwórz plik i dodaj go w następujący sposób.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 wirtualny adres IP
Dodawanie wirtualnego adresu IP zasobu. Utwórz plik i Dodaj tekst poniżej.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Dodaj konfigurację do klastra.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 sprawdzanie poprawności zasobów

Po uruchomieniu polecenia *crm_mon*można wyświetlić te dwa zasoby.
](media/HowToHLI/HASetupWithStonith/crm_mon_command.png) ![crm_mon_command. png

Ponadto można zobaczyć stan w *https://\<node adres IP >: 7630/CIB/Live/State*

![hawlk-status-Page. png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. testowanie procesu przełączania do trybu failover
Aby przetestować proces trybu failover, Zatrzymaj usługę Pacemaker na Węzeł1 i zasoby trybu failover z systemem Węzeł2.
```
Service pacemaker stop
```
Teraz Zatrzymaj usługę Pacemaker na **Węzeł2** i zasoby przełączone w tryb failover do **Węzeł1**

**Przed przejściem w tryb failover**  
![Before-failover. png ](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Po przejściu w tryb failover**  
![after-failover. png ](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-Mon-After-failover. png ](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Rozwiązywanie problemów
W tej sekcji opisano kilka scenariuszy niepowodzeń, które można napotkać podczas instalacji. Te problemy mogą nie być takie same.

### <a name="scenario-1-cluster-node-not-online"></a>Scenariusz 1: węzeł klastra nie jest w trybie online
Jeśli którykolwiek z węzłów nie jest wyświetlany w trybie online w Menedżerze klastra, możesz spróbować wykonać następujące czynności, aby przełączyć go w tryb online.

Uruchom usługę iSCSI
```
service iscsid start
```

Teraz powinno być możliwe zalogowanie się do tego węzła iSCSI
```
iscsiadm -m node -l
```
Oczekiwane dane wyjściowe wyglądają jak poniżej
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenariusz 2: YaST2 nie pokazuje widoku graficznego
Graficzny ekran YaST2 służy do konfigurowania klastra o wysokiej dostępności w tym dokumencie. Jeśli YaST2 nie jest otwarty z oknem graficznym, jak pokazano i zgłosić błąd QT, wykonaj kroki w następujący sposób. Jeśli zostanie on otwarty z oknem graficznym, możesz pominąć te czynności.

**Porn**

![YaST2-QT-GUI-Error. png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Oczekiwane dane wyjściowe**

![YaST-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Jeśli YaST2 nie jest otwarty w widoku graficznym, wykonaj poniższe czynności.

Zainstaluj wymagane pakiety. Użytkownik musi być zalogowany jako użytkownik "root" i mieć ustawioną wartość SMT, aby pobierać/instalować pakiety.

Aby zainstalować pakiety, użyj programu YaST > Software > Management Software > zależności > opcji "Zainstaluj zalecane pakiety...". Poniższy zrzut ekranu ilustruje oczekiwane ekrany.
>[!NOTE]
>Należy wykonać kroki na obu węzłach, aby można było uzyskać dostęp do widoku graficznego YaST2 z obu węzłów.

![YaST-sofwaremanagement. png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

W obszarze zależności wybierz opcję "Zainstaluj zalecane pakiety" ![yast-Dependencies. png ](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Przejrzyj zmiany i naciśnij przycisk OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Instalacja pakietu jest wykonywana ![yast-Performing-Installation. png ](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Kliknij przycisk Dalej

![YaST-Installation-Report. png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Kliknij przycisk Zakończ.

Należy również zainstalować pakiety libqt4 i libyui-QT.
```
zypper -n install libqt4
```
![zypper-Install-libqt4. png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-Install-ligyui. png ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![zypper-Install-ligyui_part2. png ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 powinna być w stanie otworzyć widok graficzny teraz, jak pokazano poniżej.
![yast2-Control-Center. png ](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenariusz 3: YaST2 nie ma opcji wysokiej dostępności
Aby opcja wysokiej dostępności była widoczna w centrum sterowania YaST2, należy zainstalować dodatkowe pakiety.

Za pomocą programu Yast2 > Software > Management Software > Wybierz następujące wzorce

- Baza SAP HANA serwera
- Język CC++ /kompilator i narzędzia
- Wysoka dostępność
- Baza serwera aplikacji SAP

Na poniższym ekranie przedstawiono kroki instalacji wzorców.

Korzystanie z programu YaST2 > oprogramowania > zarządzania oprogramowaniem

![YaST2-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Wybierz wzorce

![yast-pattern1. png ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![yast-pattern2. png ](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Kliknij przycisk **Akceptuj**

![YaST-Changed-Packages. png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Kliknij przycisk **Kontynuuj** .

![YaST2-Performing-Installation. png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Po zakończeniu instalacji kliknij przycisk **dalej** .

![YaST2-Installation-Report. png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenariusz 4: instalacja platformy HANA kończy się niepowodzeniem z powodu błędu zestawów w usłudze w zatoce
Instalacja platformy HANA kończy się niepowodzeniem z powodu następującego błędu.

![Hana-Installation-Error. png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Aby rozwiązać ten problem, należy zainstalować biblioteki (libgcc_sl i libstdc + + 6) w następujący sposób.

![zypper-Install-lib. png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenariusz 5: usługa Pacemaker kończy się niepowodzeniem

Wystąpił następujący problem podczas uruchamiania usługi Pacemaker.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Aby rozwiązać ten problem, usuń następujący wiersz z pliku */usr/lib/systemd/system/fstrim.Timer*

```
Persistent=true
```

![Persistent. png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenariusz 6: węzeł 2 nie może dołączyć do klastra

Podczas dołączania Węzeł2 do istniejącego klastra przy użyciu polecenia *ha-Cluster-Join* wystąpił następujący błąd.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-Cluster-Join-Error. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Aby rozwiązać ten problem, uruchom następujące polecenie na obu węzłach.

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-Węzeł1. Format](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-Węzeł2. Format](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Po powyższej poprawki Węzeł2 powinien zostać dodany do klastra

![ha-Cluster-Join-Fix. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. ogólna dokumentacja
Więcej informacji na temat konfiguracji SUSE HA można znaleźć w następujących artykułach: 

- [Scenariusz optymalizacji wydajności SAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Ogrodzenie oparte na magazynie](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog — korzystanie z klastra Pacemaker dla SAP HANA — część 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog — korzystanie z klastra Pacemaker dla SAP HANA — część 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
