import socket
import datetime
import pickle
import errno 

ip=socket.gethostname()
s=socket.socket()
s.connect((ip, 9686))
s.setblocking(0)
f=open('clientdata.txt','ab')


username=input('enter username:')

s.send(username.encode())

while True:
	send_msg = input('you:')
	if send_msg:
		l=[username, send_msg, datetime.datetime.now(),]
		send_data=pickle.dumps(l)
		s.send(send_data)
		pickle.dump(l,f)
	try:
		recieved_msg=s.recv(1000)
		s.setblocking(0)
		recieved_data=pickle.loads(recieved_msg)
		recieved_data=pickle.loads(recieved_data)
		print(recieved_data[0],':',recieved_data[1])
		pickle.dump(recieved_msg,f)
	except IOError as e:
		if e.errno != errno.EAGAIN and e.errno != errno.EWOULDBLOCK:
			print('reading error',str(e))
