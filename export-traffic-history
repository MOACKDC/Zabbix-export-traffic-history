import sys
import datetime
import pandas as pd
from pyzabbix import ZabbixAPI

zabbix_url = 'ZABBIX_URL'
zabbix_username = 'ZABBIX_USERNAME'
zabbix_password = 'ZABBIX_PASSWORD'

zabbix_api = ZabbixAPI(zabbix_url)
zabbix_api.login(zabbix_username, zabbix_password)

start_time = datetime.datetime.now() - datetime.timedelta(days=30)
end_time = datetime.datetime.now()

itemids = {
    "in": sys.argv[1],
    "out": sys.argv[2]
}

def get_history(itemid):
    return zabbix_api.do_request('history.get', {
        'itemids': [itemid],
        'time_from': start_time.timestamp(),
        'time_till': end_time.timestamp(),
        'output': 'extend',
        'sortfield': 'clock',
        'sortorder': 'ASC',
        'history': 3,
    })['result']

in_history = get_history(itemids["in"])
out_history = get_history(itemids["out"])

def process_history(history):
    data = []
    for entry in history:
        timestamp = int(entry['clock'])
        dt = datetime.datetime.fromtimestamp(timestamp)
        
        if dt.minute % 5 == 0:
            dt_formatted = dt.strftime('%Y-%m-%d %H:%M')
            value = float(entry['value']) / (1000 * 1000)  # Convert to Mbps
            data.append((dt_formatted, value))
    return data

in_data = process_history(in_history)
out_data = process_history(out_history)

def merge_data(in_data, out_data):
    merged_data = []
    for in_item, out_item in zip(in_data, out_data):
        dt, in_value = in_item
        _, out_value = out_item
        max_value = max(in_value, out_value)
        merged_data.append((dt, in_value, out_value, max_value))
    return merged_data

merged_data = merge_data(in_data, out_data)

# Save data to Excel
data_frame = pd.DataFrame(merged_data, columns=["Timestamp", "In (Mbps)", "Out (Mbps)", "Max (Mbps)"])
data_frame.to_excel("port_traffic_data.xlsx", index=False)
