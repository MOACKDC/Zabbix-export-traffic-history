# Zabbix-export-traffic-history
Using the zabbix API, export the traffic values for every 5 minutes for 30 days and save them as xlsx files

Installing Dependencies:  
`pip install pyzabbix pandas openpyx`

Run  
`python3 export-traffic-history.py <item_id_1> <item_id_2>`

<item_id_1> = ZABBIX_PORT_IN_item_id  
<item_id_2> = ZABBIX_PORT_OUT_item_id  

You can find item ID in Configuration->Hosts->Devices->Item
