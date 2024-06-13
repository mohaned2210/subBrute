# subBrute
brute multi domain
```
اللغة العربية في اخر الصفحة
```
```
Modifications:
1- can the input file contain multi domain.
2- you can add resolver file
3- you can add proxy ip
4- more useragent
5- faster
6- Estimated finish time, update every 5 m
```
installation:

```
git clone https://github.com/aashishsec/subBruter.git
cd subBruter
pip install .
```
then change the subBruter.py code to bellow code
```
import requests
import colorama
import random
import argparse
import concurrent.futures
import httpx
from datetime import datetime, timedelta
from colorama import Fore, Style
import threading as th
import logging
import signal
import sys

colorama.init(autoreset=True)

# Set up logging
logging.basicConfig(level=logging.CRITICAL)  # Suppress all logging

# Set httpx to critical level to suppress its logs
httpx_log = logging.getLogger("httpx")
httpx_log.setLevel(logging.CRITICAL)

green = Fore.GREEN
magenta = Fore.MAGENTA
cyan = Fore.CYAN
mixed = Fore.RED + Fore.BLUE
red = Fore.RED
blue = Fore.BLUE
yellow = Fore.YELLOW
white = Fore.WHITE
reset = Style.RESET_ALL

colors = [magenta, cyan, mixed, red, blue, yellow, white]
random_color = random.choice(colors)

bold = Style.BRIGHT

USER_AGENT = [
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.135 Safari/537.36 Edge/12.246",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_2) AppleWebKit/601.3.9 (KHTML, like Gecko) Version/9.0.2 Safari/601.3.9",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/99.0.1150.36",
"Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:99.0) Gecko/20100101 Firefox/99.0",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 Edge/16.16299",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.3 Safari/602.3.12",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36 Edge/14.14931",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.107 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.3; Win64; x64; Trident/7.0; rv:11.0) like Gecko",
"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.96 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.97 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:89.0) Gecko/20100101 Firefox/89.0",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/9.1.2 Safari/601.7.7",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.3 Safari/602.3.12",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.96 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/8.1.2 Safari/601.7.7",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:89.0) Gecko/20100101 Firefox/89.0",
]
random_user_agent = random.choice(USER_AGENT)

header = {"User-Agent": random_user_agent}

output_lock = th.Lock()



def banner():
    print(f'''{bold}{random_color}
░██████╗██╗░░░██╗██████╗░██████╗░██████╗░██╗░░░██╗████████╗███████╗██████╗░
██╔════╝██║░░░██║██╔══██╗██╔══██╗██╔══██╗██║░░░██║╚══██╔══╝██╔════╝██╔══██╗
╚█████╗░██║░░░██║██████╦╝██████╦╝██████╔╝██║░░░██║░░░██║░░░█████╗░░██████╔╝
░╚═══██╗██║░░░██║██╔══██╗██╔══██╗██╔══██╗██║░░░██║░░░██║░░░██╔══╝░░██╔══██╗
██████╔╝╚██████╔╝██████╦╝██████╦╝██║░░██║╚██████╔╝░░░██║░░░███████╗██║░░██║
╚═════╝░░╚═════╝░╚═════╝░╚═╝░░╚═╝░╚═╝░░╚═╝░╚═════╝░░░░╚═╝░░░╚══════╝╚═╝░░╚═╝
        Author   : Aashish💕💕  
        Github   : https://github.com/aashishsec
        edit by  : Network4x
        twitter  : https://x.com/network4x
      ''')
    print("-" * 80)
    print(f"{bold}{random_color}subBruteForcer starting at {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}")
    print("-" * 80)

parser = argparse.ArgumentParser(
    description=f"{bold}{random_color} subBruteForcer is a tool designed to efficiently probe for alive subdomains from a provided wordlist.")

parser.add_argument('-d', '--domain_file', metavar='domain_file', type=str, required=True,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of domains to brute force.")
parser.add_argument('-w', '--wordlist', metavar='wordlist', help=f"[{bold}{random_color}INFO]: {bold}{random_color}List of wordlist.", default="subdomains.txt")
parser.add_argument('-o', '--output', metavar='output', type=str, default="subBruteForcer.txt", required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File to save our output.")
parser.add_argument("-t", "--threads", help=f"[{bold}INFO{random_color}]: {random_color}{random_color}Threading level to make fast process.", type=int, default=50)
parser.add_argument('-p', '--proxies', metavar='proxies', type=str, required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of proxies.")
parser.add_argument('-r', '--resolvers', metavar='resolvers', type=str, required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of resolvers.")

args = parser.parse_args()

domain_file = args.domain_file
output = args.output
threads = args.threads
wordlist = args.wordlist
proxy_file = args.proxies
resolver_file = args.resolvers

global_output = []
global_words = []
proxies = []
resolvers = []

if proxy_file:
    with open(proxy_file, 'r') as f:
        proxies = f.read().splitlines()

if resolver_file:
    with open(resolver_file, 'r') as f:
        resolvers = f.read().splitlines()

def get_proxy():
    if proxies:
        return random.choice(proxies)
    return None

def get_resolver():
    if resolvers:
        return random.choice(resolvers)
    return None

def sub_brute(domain, word):
    global global_output
    if domain.startswith("https://") or domain.startswith("http://"):
        url = f"{word}.{domain}"
    else:
        url = f"https://{word}.{domain}"

    try:
        proxy = get_proxy()
        resolver = get_resolver()
        proxies_dict = None

        if proxy:
            protocol = proxy.split('://')[0]
            proxies_dict = {protocol: proxy}

        transport = httpx.HTTPTransport(retries=3)
        with httpx.Client(verify=False, timeout=10, follow_redirects=True, headers=header, proxies=proxies_dict, transport=transport) as client:
            request = client.get(url)
        status_code = request.status_code
        content_length = request.headers.get('Content-Length')

        with output_lock:
            if status_code == 200:
                if content_length is not None:
                    print(f"{green}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{green}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 301 or status_code == 302:
                if content_length is not None:
                    print(f"{yellow}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{yellow}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 403:
                if content_length is not None:
                    print(f"{red}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{red}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 404 or status_code == 429 or status_code == 504 or status_code == 502:
                pass
            else:
                if content_length is not None:
                    print(f"{random_color}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{random_color}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
    except KeyboardInterrupt:
        print(f"[{blue}INFO{random_color}]: subBruteForcer says BYE!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

def save_output(output):
    with output_lock:
        with open(output, 'w') as file:
            file.writelines(global_output)

def threading_func(domain, paths, start_time, total_tasks):
    completed_tasks = 0
    next_update_time = start_time + timedelta(minutes=5)
    times = []

    try:
        with concurrent.futures.ThreadPoolExecutor(max_workers=threads * 2) as executor:
            futures = [executor.submit(sub_brute, domain, path) for path in paths]
            for future in concurrent.futures.as_completed(futures):
                completed_tasks += 1
                if datetime.now() >= next_update_time:
                    elapsed_time = datetime.now() - start_time
                    times.append(elapsed_time.total_seconds() / completed_tasks)
                    if len(times) > 5:  # Keep the last 5 time estimates for averaging
                        times.pop(0)
                    avg_task_time = sum(times) / len(times)
                    remaining_tasks = total_tasks - completed_tasks
                    remaining_time_seconds = avg_task_time * remaining_tasks
                    remaining_time = timedelta(seconds=remaining_time_seconds)
                    remaining_hours, remainder = divmod(remaining_time.total_seconds(), 3600)
                    remaining_minutes, _ = divmod(remainder, 60)
                    print(f"{bold}{yellow}Estimated time remaining: {int(remaining_hours)}:{int(remaining_minutes):02d}")
                    next_update_time += timedelta(minutes=5)
        concurrent.futures.wait(futures)
    except KeyboardInterrupt:
        print(f"[{bold}INFO{random_color}]: subBruteForcer says exit!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

def main():
    banner()
    global global_words

    try:
        with open(wordlist, "r") as p:
            paths = p.read().splitlines()
            for p in paths:
                global_words.append(p)

        print(f"{bold}{random_color}[*] No.of Words".ljust(20, " "), ":", len(global_words))
        print("-" * 80)

        with open(domain_file, "r") as file:
            domains = file.read().splitlines()

        total_tasks = len(domains) * len(global_words)
        start_time = datetime.now()

        domain_batches = [domains[i:i + 10] for i in range(0, len(domains), 10)]

        for batch in domain_batches:
            threads = []
            for domain in batch:
                t = th.Thread(target=threading_func, args=(domain, global_words, start_time, total_tasks))
                threads.append(t)
                t.start()

            for t in threads:
                t.join()

        save_output(output)

    except KeyboardInterrupt:
        print(f"[{blue}INFO{random_color}]: subBruteForcer says exit!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

# Handle termination signals
def signal_handler(sig, frame):
    print(f"[{blue}INFO{random_color}]: subBruteForcer says exit!")
    save_output(output)
    sys.exit()

signal.signal(signal.SIGINT, signal_handler)
signal.signal(signal.SIGTERM, signal_handler)

if __name__ == "__main__":
    main()

```
then save change and run the tool
```
python3 subBruter.py -h
```
note for proxy file should be like this:
```
#sample
http://178.16.130.81:80
http://1.180.51.194:8800
socks4://103.75.228.14:6093
socks5://174.77.111.198:49547
https://1.180.61.194:8800
```
```
take proxy from here:

https://github.com/TheSpeedX/PROXY-List or https://proxyscrape.com/free-proxy-list

and put proxy in this website to make sure its work:

https://hide.mn/en/proxy-checker/

youtube explain for proxychains:
https://youtu.be/KWwOU1z5E8E?si=u4XRHSoqZ9hy_bfW
```

credit:
Aashish
chatgpt
---------------------------------------------------------------------------------------------------------------
التعديلات:

اسرع بعشر مرات , يدعم ملف فيه دومينات , يدعم الريسولفر , يدعم البروكسي , يعطيك وقت تقريبي للانتهاء كل خمس دقايق , يوزر ايجنت اكثر
طريقة التحميل
```
git clone https://github.com/aashishsec/subBruter.git
cd subBruter
pip install .
```
ثم غير كود subBruter.py الى الكود هذا:
```
import requests
import colorama
import random
import argparse
import concurrent.futures
import httpx
from datetime import datetime, timedelta
from colorama import Fore, Style
import threading as th
import logging
import signal
import sys

colorama.init(autoreset=True)

# Set up logging
logging.basicConfig(level=logging.CRITICAL)  # Suppress all logging

# Set httpx to critical level to suppress its logs
httpx_log = logging.getLogger("httpx")
httpx_log.setLevel(logging.CRITICAL)

green = Fore.GREEN
magenta = Fore.MAGENTA
cyan = Fore.CYAN
mixed = Fore.RED + Fore.BLUE
red = Fore.RED
blue = Fore.BLUE
yellow = Fore.YELLOW
white = Fore.WHITE
reset = Style.RESET_ALL

colors = [magenta, cyan, mixed, red, blue, yellow, white]
random_color = random.choice(colors)

bold = Style.BRIGHT

USER_AGENT = [
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/42.0.2311.135 Safari/537.36 Edge/12.246",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_2) AppleWebKit/601.3.9 (KHTML, like Gecko) Version/9.0.2 Safari/601.3.9",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.111 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36 Edg/99.0.1150.36",
"Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.75 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:99.0) Gecko/20100101 Firefox/99.0",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 Edge/16.16299",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.3 Safari/602.3.12",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36 Edge/14.14931",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.107 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.3; Win64; x64; Trident/7.0; rv:11.0) like Gecko",
"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.96 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.97 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:89.0) Gecko/20100101 Firefox/89.0",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/9.1.2 Safari/601.7.7",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3",
"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:91.0) Gecko/20100101 Firefox/91.0",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.3 Safari/602.3.12",
"Mozilla/5.0 (X11; CrOS x86_64 8172.45.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.64 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3",
"Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36",
"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.112 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36",
"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36",
"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.96 Safari/537.36",
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/8.1.2 Safari/601.7.7",
"Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:89.0) Gecko/20100101 Firefox/89.0",
]
random_user_agent = random.choice(USER_AGENT)

header = {"User-Agent": random_user_agent}

output_lock = th.Lock()



def banner():
    print(f'''{bold}{random_color}
░██████╗██╗░░░██╗██████╗░██████╗░██████╗░██╗░░░██╗████████╗███████╗██████╗░
██╔════╝██║░░░██║██╔══██╗██╔══██╗██╔══██╗██║░░░██║╚══██╔══╝██╔════╝██╔══██╗
╚█████╗░██║░░░██║██████╦╝██████╦╝██████╔╝██║░░░██║░░░██║░░░█████╗░░██████╔╝
░╚═══██╗██║░░░██║██╔══██╗██╔══██╗██╔══██╗██║░░░██║░░░██║░░░██╔══╝░░██╔══██╗
██████╔╝╚██████╔╝██████╦╝██████╦╝██║░░██║╚██████╔╝░░░██║░░░███████╗██║░░██║
╚═════╝░░╚═════╝░╚═════╝░╚═╝░░╚═╝░╚═╝░░╚═╝░╚═════╝░░░░╚═╝░░░╚══════╝╚═╝░░╚═╝
        Author   : Aashish💕💕  
        Github   : https://github.com/aashishsec
        edit by  : Network4x
        twitter  : https://x.com/network4x
      ''')
    print("-" * 80)
    print(f"{bold}{random_color}subBruteForcer starting at {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}")
    print("-" * 80)

parser = argparse.ArgumentParser(
    description=f"{bold}{random_color} subBruteForcer is a tool designed to efficiently probe for alive subdomains from a provided wordlist.")

parser.add_argument('-d', '--domain_file', metavar='domain_file', type=str, required=True,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of domains to brute force.")
parser.add_argument('-w', '--wordlist', metavar='wordlist', help=f"[{bold}{random_color}INFO]: {bold}{random_color}List of wordlist.", default="subdomains.txt")
parser.add_argument('-o', '--output', metavar='output', type=str, default="subBruteForcer.txt", required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File to save our output.")
parser.add_argument("-t", "--threads", help=f"[{bold}INFO{random_color}]: {random_color}{random_color}Threading level to make fast process.", type=int, default=50)
parser.add_argument('-p', '--proxies', metavar='proxies', type=str, required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of proxies.")
parser.add_argument('-r', '--resolvers', metavar='resolvers', type=str, required=False,
                    help=f"[{bold}{random_color}INFO]: {bold}{random_color}File containing list of resolvers.")

args = parser.parse_args()

domain_file = args.domain_file
output = args.output
threads = args.threads
wordlist = args.wordlist
proxy_file = args.proxies
resolver_file = args.resolvers

global_output = []
global_words = []
proxies = []
resolvers = []

if proxy_file:
    with open(proxy_file, 'r') as f:
        proxies = f.read().splitlines()

if resolver_file:
    with open(resolver_file, 'r') as f:
        resolvers = f.read().splitlines()

def get_proxy():
    if proxies:
        return random.choice(proxies)
    return None

def get_resolver():
    if resolvers:
        return random.choice(resolvers)
    return None

def sub_brute(domain, word):
    global global_output
    if domain.startswith("https://") or domain.startswith("http://"):
        url = f"{word}.{domain}"
    else:
        url = f"https://{word}.{domain}"

    try:
        proxy = get_proxy()
        resolver = get_resolver()
        proxies_dict = None

        if proxy:
            protocol = proxy.split('://')[0]
            proxies_dict = {protocol: proxy}

        transport = httpx.HTTPTransport(retries=3)
        with httpx.Client(verify=False, timeout=10, follow_redirects=True, headers=header, proxies=proxies_dict, transport=transport) as client:
            request = client.get(url)
        status_code = request.status_code
        content_length = request.headers.get('Content-Length')

        with output_lock:
            if status_code == 200:
                if content_length is not None:
                    print(f"{green}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{green}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 301 or status_code == 302:
                if content_length is not None:
                    print(f"{yellow}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{yellow}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 403:
                if content_length is not None:
                    print(f"{red}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{red}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
            elif status_code == 404 or status_code == 429 or status_code == 504 or status_code == 502:
                pass
            else:
                if content_length is not None:
                    print(f"{random_color}(Status: {status_code}) --[Size: {content_length}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {content_length}]---> {url}\n")
                else:
                    print(f"{random_color}(Status: {status_code}) --[Size: {len(request.content)}]---> {url}")
                    global_output.append(f"(Status: {status_code}) --[Size: {len(request.content)}]---> {url}\n")
    except KeyboardInterrupt:
        print(f"[{blue}INFO{random_color}]: subBruteForcer says BYE!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

def save_output(output):
    with output_lock:
        with open(output, 'w') as file:
            file.writelines(global_output)

def threading_func(domain, paths, start_time, total_tasks):
    completed_tasks = 0
    next_update_time = start_time + timedelta(minutes=5)
    times = []

    try:
        with concurrent.futures.ThreadPoolExecutor(max_workers=threads * 2) as executor:
            futures = [executor.submit(sub_brute, domain, path) for path in paths]
            for future in concurrent.futures.as_completed(futures):
                completed_tasks += 1
                if datetime.now() >= next_update_time:
                    elapsed_time = datetime.now() - start_time
                    times.append(elapsed_time.total_seconds() / completed_tasks)
                    if len(times) > 5:  # Keep the last 5 time estimates for averaging
                        times.pop(0)
                    avg_task_time = sum(times) / len(times)
                    remaining_tasks = total_tasks - completed_tasks
                    remaining_time_seconds = avg_task_time * remaining_tasks
                    remaining_time = timedelta(seconds=remaining_time_seconds)
                    remaining_hours, remainder = divmod(remaining_time.total_seconds(), 3600)
                    remaining_minutes, _ = divmod(remainder, 60)
                    print(f"{bold}{yellow}Estimated time remaining: {int(remaining_hours)}:{int(remaining_minutes):02d}")
                    next_update_time += timedelta(minutes=5)
        concurrent.futures.wait(futures)
    except KeyboardInterrupt:
        print(f"[{bold}INFO{random_color}]: subBruteForcer says exit!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

def main():
    banner()
    global global_words

    try:
        with open(wordlist, "r") as p:
            paths = p.read().splitlines()
            for p in paths:
                global_words.append(p)

        print(f"{bold}{random_color}[*] No.of Words".ljust(20, " "), ":", len(global_words))
        print("-" * 80)

        with open(domain_file, "r") as file:
            domains = file.read().splitlines()

        total_tasks = len(domains) * len(global_words)
        start_time = datetime.now()

        domain_batches = [domains[i:i + 10] for i in range(0, len(domains), 10)]

        for batch in domain_batches:
            threads = []
            for domain in batch:
                t = th.Thread(target=threading_func, args=(domain, global_words, start_time, total_tasks))
                threads.append(t)
                t.start()

            for t in threads:
                t.join()

        save_output(output)

    except KeyboardInterrupt:
        print(f"[{blue}INFO{random_color}]: subBruteForcer says exit!")
        save_output(output)
        sys.exit()
    except Exception as e:
        pass

# Handle termination signals
def signal_handler(sig, frame):
    print(f"[{blue}INFO{random_color}]: subBruteForcer says exit!")
    save_output(output)
    sys.exit()

signal.signal(signal.SIGINT, signal_handler)
signal.signal(signal.SIGTERM, signal_handler)

if __name__ == "__main__":
    main()

```
احفظ التعديلات وشغل التول
```
python3 subBruter.py -h
```
ملاحضة ملف البروكسي لازم يكون نفس كذا:
```
#sample
http://178.16.130.81:80
http://1.180.51.194:8800
socks4://103.75.228.14:6093
socks5://174.77.111.198:49547
https://1.180.61.194:8800
```
```
خذ البروكسي من هنا
https://github.com/TheSpeedX/PROXY-List or https://proxyscrape.com/free-proxy-list

ثم حط البروكسي في هذا الموقع عشان تتأكد انه شغال
https://hide.mn/en/proxy-checker/

شرح يوتيوب للبروكسي
https://youtu.be/KWwOU1z5E8E?si=u4XRHSoqZ9hy_bfW
```
