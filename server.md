mport socket
import datetime
import select
import time
import pickle


ip=socket.gethostname()

s=socket.socket()
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR,1)
s.settimeout(1)
s.bind((ip, 9686))
s.listen(5)

header=10

s_list= [s,]
account_data=[]

f = open('serverdata.txt','ab')

def check_recv():
	global s_list
	for i in s_list:
		try:
			time.sleep(1)
			recvd_msg= i.recv(690)
			s.settimeout(0)
			if recvd_msg == False:
				return "Offline"
			else:
				return recvd_msg , i
		except:
			pass

def giv_recv_msg(x):
	recvd_msg=pickle.loads(x)
	return recvd_msg


def send_msg(x):
	for i in account_data:
		if i[0]== x[1]:
			continue
		else:
			sending_msg=pickle.dumps(x[0])
			i[0].send(sending_msg)




while True:	
	readable , _ , exceptional = select.select( s_list , [], s_list,)
	for i in readable:
	 	if i == s:
	 		c , c_addr= s.accept()

	 		message= c.recv(20)
	 		c.settimeout(1)

	 		if message ==0:
	 			continue
		 	s_list.append(c)
		 	account_data.append([c,message])
		 	print(account_data)

	else:
		try:
			data=check_recv()
			
			msg=giv_recv_msg(data[0])
			print(msg)
			s.settimeout(1)
			send_msg(data)
		except:
			pass
	
