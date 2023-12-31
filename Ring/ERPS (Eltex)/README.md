Основные понятия и термины:
- RPL (Ring Protection Link) - резервный линк между коммутаторами, который будет заблокирован механизмом ERPS при нормальном режиме работы кольца (IDLE);
- RPL Owner - коммутатор, подключенный к RPL, который блокирует RPL Link при нормальном режиме работы кольца и разблокирует его при разрыве кольца;
- RPL Neighbor - коммутатор, напрямую связанный с RPL Owner через RPL Link. Также блокирует RPL Link при нормальном режиме работы кольца и разблокирует его при разрыве кольца;

- R-APS (Ring - Automatic Protection Switching) - протокол обмена сообщениями, определенный в Y.1731 и в G.8032. Используется для координации действий по защите кольца;
- RAPS VLAN (A-RPS Channel) - служебный VLAN, в котором передаются R-APS PDUs;
- Protected VLAN - VLANs для передачи DATA трафика. RPL Link блокирует передачу только в Protected VLAN.

Таймеры:
- Hold-Off Timer - используется при падении линка. На коммутаторах кольца, зафиксировавших аварию, запускается Hold-Off Timer, по истечении которого отправляется R-APS PDU Signal Fail. По дефолту таймер настроен на 0 сек;
- Guard Timer - Запускается на коммутаторах, фиксирующих изменение топологии (падение/поднятие линка). При этом отправляется R-APS PDU и запускается Guard Timer, до тех пор пока таймер не истек, все входящие R-APS pdu игнорируются. По дефолту таймер настроен на 500 мсек;
- WTR Timer - используется только RPL Owner для отложенной блокировки RPL порта. Таймер истечёт только в случае отсутствия изменений в топологии за время своего действия. По дефолту таймер настроен на 5 мин;
- Periodic Timer - интервал отправки R-APS PDUs.
