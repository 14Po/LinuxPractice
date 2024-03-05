
## ��� 1: �������� ����������� ����� � PWD

� ������� ������������ `https://labs.play-with-docker.com/` ������� ��� ����������� ������ ��� ������ ������� `ADD NEW INSTANCE`(��. ���. 1).

<p align="center">
<img width=60% src = "pic/vm.png">
</p>

<p align="center">
  ������� 1 - ��� ����������� ������ � Play With Docker
</p>

## ��� 2: ��������� SSH-�����
��� ����������� � ����������� ������� �� ssh ���������� �������������� ���� (��. ���. 2).
 
<p align="center">
<img width=100% src = "pic/shhKeyGen.png">
</p>

<p align="center">
  ������� 2 - ��������� ����� ssh ���������� ed25519
</p>

����������� � ����������� ������� ���������� �� ��������, ��������� ��� ������ �� � PWD:

1. `ssh ip172-18-0-40-cnibn7q91nsg00b8iieg@direct.labs.play-with-docker.com`
2. `ssh ip172-18-0-50-cnibn7q91nsg00b8iieg@direct.labs.play-with-docker.com`
3. `ssh ip172-18-0-56-cnibn7q91nsg00b8iieg@direct.labs.play-with-docker.com`

## ��� 3: ��������� ���������
�������� �����, ����������� �� ������� 3, ���������� ��������� ����: 
* � Linux A � Linux C - �� 1 ��������
* Linux B  - �� 2 �������� 

<p align="center">
<img width=100% src = "pic/Linux Practice.png">
</p>

<p align="center">
  ������� 3 - ��������� ���� �� �� �������� �����
</p>

�� ������� ������� �������� ���������� ��������� ��������� ip-������:
Linux A : `192.168.14.10 / 24` (��. ���. 4)
 
Linux � : `192.168.11.100 / 24` (��. ���. 5)

Linux B_1 : `192.168.14.1 / 24` (��. ���. 6)

Linux B_2 : `192.168.11.1 / 24` (��. ���. 7)

<!-------------------------------------------------------->

<p align="center">
<img width=100% src = "pic/Avlan.png">
</p>

<p align="center">
  ������� 4 - �������� �������� � ��������� IP - ������ ��� Linux A
</p>

<!-------------------------------------------------------->

<p align="center">
<img width=100% src = "pic/Cvlan.png">
</p>

<p align="center">
  ������� 5 - �������� �������� � ��������� IP - ������ ��� Linux C
</p>

<!-------------------------------------------------------->

<p align="center">
<img width=100% src = "pic/Bvlan1.png">
</p>

<p align="center">
  ������� 6 - �������� 1-�� �������� � ��������� IP - ������ ��� Linux B 
</p>

<!-------------------------------------------------------->

<p align="center">
<img width=100% src = "pic/Bvlan2.png">
</p>

<p align="center">
  ������� 7 - �������� 2-�� �������� � ��������� IP - ������ ��� Linux B 
</p>

<!-------------------------------------------------------->

 ����� ��������� ���� �� �� ���������� ��������� �������� � �������� � � � � �� �������� ����� ������ � ��� ������ �������: `ip route add <subnet A vm>/<mask> via <gateway ip B vm>` (��. ���. 8, 9).

<!-------------------------------------------------------->
<p align="center">
<img width=60% src = "pic/A_route.png">
</p>

<p align="center">
  ������� 8 - ��������� �������� �������� �� � � � ����� B 
</p>
<!-------------------------------------------------------->


<!-------------------------------------------------------->
<p align="center">
<img width=60% src = "pic/C_route.png">
</p>

<p align="center">
  ������� 9 -  ��������� �������� �������� �� C � A ����� B
</p>
<!-------------------------------------------------------->

## ��� 3: ����������� ������-���������� �������������� ����� � � � 

����� ��������� ��������� ���������� ���������� ������ �� ����� � (���������� =3 ). ��� ����� ����� ������������ ���������� Flask (��. ���. 10)

<!-------------------------------------------------------->
<p align="center">
<img width=100% src = "pic/installFlask.png">
</p>

<p align="center">
  ������� 10 - ��������� Flask
</p>
<!-------------------------------------------------------->

����� ������� ���� app.py �� ��������� ����������:

```py
from flask import Flask

app = Flask(__name__)
#��������� GET �������
@app.route("/")
def get_():
    return "��� ������� GET ������\n\n"

#��������� POST �������
@app.route("/<number>", methods =['POST'])
def post_(number):
    return f"POST ������ : {number}\n\n"

#��������� PUT �������
@app.route("/", methods =['PUT'])
def put():
    return "��� ������� PUT ������\n\n"

app.run(host='0.0.0.0', port=5000)
```
���������� ������ �������� ���: 
<!-------------------------------------------------------->
<p align="center">
<img width=100% src = "pic/ServerArunning.png">
</p>

<p align="center">
  ������� 12 -  ������ �� ������ �
</p>
<!-------------------------------------------------------->

<!-------------------------------------------------------->
<p align="center">
<img width=60% src = "pic/C_client_get_post_put.png">
</p>

<p align="center">
  ������� 12  -  ����� �� ������������ ������� � ������ � �� �
</p>
<!-------------------------------------------------------->


<!-------------------------------------------------------->
<p align="center">
<img width=80% src = "pic/A_server_requests.png">
</p>

<p align="center">
  ������� 13 - �������, ��������� �� ������ �
</p>
<!-------------------------------------------------------->

��� ����� ��������� ������ � ������������ �������� ������� ��� �������.

*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux A
ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.14.10/24
ip link set macvlan1 up
ip route add 192.168.11.0/24 via 192.168.14.1

pip install flask

touch app.py
cat << EOF >app.py

from flask import Flask

app = Flask(__name__)

@app.route("/")
def get_():
    return "��� ������� GET ������\n\n"

@app.route("/<number>", methods =['POST'])
def post_(number):
    return f"POST ������ : {number}\n\n"

@app.route("/", methods =['PUT'])
def put():
    return "��� ������� PUT ������\n\n"

app.run(host='0.0.0.0', port=5000)

EOF

python app.py
```
*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux B

ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.14.1/24
ip link set macvlan1 up

ip link add macvlan2 link eth0 type macvlan mode bridge
ip addres add dev macvlan2 192.168.11.1/24
ip link set macvlan2 up
```
*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux �

ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.11.100/24
ip link set macvlan1 up
ip route add 192.168.14.0/24 via 192.168.11.1

echo "\n������� ������ GET"
curl "http://192.168.14.10:5000/"

echo "������� ������ POST"
curl -X POST http://192.168.14.10:5000/14

echo "������� ������ PUT"
curl -X PUT http://192.168.14.10:5000/ -d "PUT request data"
```

