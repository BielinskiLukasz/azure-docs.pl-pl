## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Tworzenie klasycznej sieci wirtualnej w portalu Azure
Aby utworzyć sieć wirtualną oparta na powyższym scenariuszu klasyczny, wykonaj następujące kroki.

1. W przeglądarce przejdź do strony http://portal.azure.com i w razie potrzeby zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **Utwórz zasób** > **sieci** > **sieci wirtualnej**. Zwróć uwagę, że **wybierz model wdrożenia** listy już pokazuje **klasycznego**. 3. Kliknij przycisk **Utwórz** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. Na **sieci wirtualnej** okienka, typ **nazwa** sieci wirtualnej, a następnie kliknij przycisk **przestrzeni adresów**. Skonfiguruj ustawienia przestrzeni adresów sieci wirtualnej i jej pierwszej podsieci, a następnie kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bloku CIDR do naszego scenariusza.
   
    ![Okienko przestrzeni adresów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Kliknij przycisk **grupy zasobów** i wybierz grupę zasobów, aby dodać sieć wirtualną do, lub kliknij przycisk **Utwórz nową grupę zasobów** można dodać sieci wirtualnej do nowej grupy zasobów. Na poniższej ilustracji przedstawiono zasobu ustawienia grupy dla nowej grupy zasobów o nazwie **TestRG**. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Utwórz okienko grupy zasobów](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. W razie potrzeby zmień wartości ustawień **Subskrypcja** i **Lokalizacja** sieci wirtualnej. 
7. Jeśli nie chcesz, aby sieć wirtualna była wyświetlana jako kafelek na **tablicy startowej**, wyłącz opcję **Przypnij do tablicy startowej**. 
8. Kliknij przycisk **Utwórz** zobaczysz Kafelek o nazwie **tworzenie sieci wirtualnej** jak pokazano na poniższej ilustracji.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Poczekaj, aż do utworzenia sieci wirtualnej, a gdy pojawi się Kafelek, kliknij go, aby dodać więcej podsieci.
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Powinny pojawić się **konfiguracji** dla sieci wirtualnej, jak pokazano. 
   
    ![Tworzenie sieci wirtualnej w portalu](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Kliknij przycisk **podsieci** > **Dodaj**, wpisz **nazwa** i określ **(blok CIDR) zakres adresów** dla podsieci, a następnie Kliknij przycisk **OK**. Na poniższej ilustracji przedstawiono ustawienia bieżącego scenariusza.
    
    ![Tworzenie sieci wirtualnej w portalu Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

