## <a name="scenario"></a>Scenariusz
Ten dokument przeprowadzi wdrożenia, który używa statycznego publicznego adresu IP przydzielone do maszyny wirtualnej (VM). W tym scenariuszu istnieje pojedynczy maszynę Wirtualną za pomocą własnego statycznego publicznego adresu IP. Maszyna wirtualna jest częścią podsieci o nazwie **frontonu** , ale ma także statycznego prywatnego adresu IP (**192.168.1.101**) w tej podsieci.

Statyczny adres IP może być konieczne dla serwerów sieci web, które Wymagaj połączeń SSL, w których certyfikat SSL jest połączony z adresu IP. 

![OPIS ILUSTRACJI](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Można wykonać poniższe kroki, aby wdrożyć w środowisku pokazanym na powyższej ilustracji.

