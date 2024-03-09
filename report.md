
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

## ��� 3: ��������� �������� 

�� Linux B ���������� ��������� ������� ����� �������, ����� Linux B ��������� ������ http � ������ ����� ���� 5000.

��� ����� ������� ��� ������ tcpdump.

`apk add tcpdump`

`tcpdump -i any -s 0 'tcp port http' -w /tmp/http.cap and 'port 5000'`

����� �����, ������ ������ � Linux A �� Linux C, ����� ����, ��������, 1234, ������� ���������:

<!-------------------------------------------------------->
<p align="center">
<img width=100% src = "pic/curl1234.png">
</p>

<p align="center">
  ������� 10 - ������ ����� ����, �������� �� 5000
</p>
<!-------------------------------------------------------->


## ��� 4: ����������� ������-���������� �������������� ����� � � � 

����� ��������� ��������� ���������� ���������� ������ �� ����� � (���������� =3 ). ��� ����� ����� ������������ ���������� Flask (��. ���. 10)

<!-------------------------------------------------------->
<p align="center">
<img width=100% src = "pic/installFlask.png">
</p>

<p align="center">
  ������� 11 - ��������� Flask
</p>
<!-------------------------------------------------------->

����� ������� ���� app.py �� ��������� ����������:

```py
from flask import Flask, request

app = Flask(__name__)

data = {"username":"", "password":""}

@app.route("/")
def get_():
    return data

@app.route("/",methods = ['POST'])
def post():
    data_json=request.get_json()
    if data_json is None:
        return 'Invalid JSON data', 400

    data['username']=data_json['username']
    data['password']=data_json['password']
    print(f"Data received {data_json}")

    return [data['username'], data['password']]

@app.route("/", methods =['PUT'])
def put():
    str = request.args.get('password')
    print(f"New password received {str}")
    data['password'] = str
    return [data['username'], data['password']]

app.run(host='0.0.0.0', port=5000)

EOF
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
<img width=90% src = "pic/C_client.png">
</p>

<p align="center">
  ������� 13  -  ����� �� ������������ ������� � ������ � �� �
</p>
<!-------------------------------------------------------->


<!-------------------------------------------------------->
<p align="center">
<img width=90% src = "pic/server_answers.png">
</p>

<p align="center">
  ������� 14 - �������, ��������� �� ������ �
</p>
<!-------------------------------------------------------->

��� ����� ��������� ������ � ������������ �������� ������� ��� �������.

*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux A
echo -e "Starting the MACVLAN installation\n"
ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.14.10/24
ip link set macvlan1 up

echo -e "Ending the MACVLAN installation\n\n"

echo -e "Routing configuration FROM Linux A to Linux C \n\n"
ip route add 192.168.11.0/24 via 192.168.14.1

echo -e "Start to installing FLASK \n"
pip install flask
echo -e "End to installing FLASK \n\n"

echo -e "Creating web-server\n"
touch app.py
cat << EOF >app.py

from flask import Flask, request

app = Flask(__name__)

data = {"username":"", "password":""}

@app.route("/")
def get_():
    return data

# @app.route("/<number>", methods =['POST'])
# def post_(number):
#     return f"POST-request : {number}\n\n"

@app.route("/",methods = ['POST'])
def post():
    data_json=request.get_json()
    if data_json is None:
        return 'Invalid JSON data', 400

    data['username']=data_json['username']
    data['password']=data_json['password']
    print(f"Data received {data_json}")

    return [data['username'], data['password']]

@app.route("/", methods =['PUT'])
def put():
    str = request.args.get('password')
    print(f"New password received {str}")
    data['password'] = str
    return [data['username'], data['password']]

app.run(host='0.0.0.0', port=5000)

EOF

echo -e "Run the server\n"
python app.py
```
*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux B

echo -e "Starting the first MACVLAN installation\n"
ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.14.1/24
ip link set macvlan1 up
echo -e "Ending the first MACVLAN installation\n\n"

echo -e "Starting the second MACVLAN installation\n"
ip link add macvlan2 link eth0 type macvlan mode bridge
ip addres add dev macvlan2 192.168.11.1/24
ip link set macvlan2 up
echo -e "Ending the second MACVLAN installation\n\n"

echo -e "Installation tcpdump\n"
apk add tcpdump

echo -e "Configurate tcpdump (only http throw 5000 port)"

tcpdump -i any -s 0 'tcp port http' -w /tmp/http.cap and 'port 5000'
```
*������ ������������ ����������� ������ �*
```sh
#!/bin/bash
# Linux С

echo -e "Starting the first MACVLAN installation\n"

ip link add macvlan1 link eth0 type macvlan mode bridge
ip address add dev macvlan1 192.168.11.100/24
ip link set macvlan1 up
ip route add 192.168.14.0/24 via 192.168.11.1

echo -e "Ending the first MACVLAN installation\n\n"

echo -e "Sending GET-request\n"
curl "http://192.168.14.10:5000/"


echo -e "Sending POST-request\n"
curl -X POST -H "Content-Type: application/json" -d' {"username":"Po","password":"xyz"}' http://192.168.14.10:5000

echo -e "\nChanging passwords \n"

curl -X PUT http://192.168.14.10:5000?password=ber453

echo -e "Sending request to incorrect port\n"
curl http://192.168.14.10:5002
```

