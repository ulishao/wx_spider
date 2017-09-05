#coding=utf8
import threading,time,random
import requests
from Queue import Queue
from lxml import etree
import re
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

count = 0

data_quere = Queue()

proxies = []
class urls():

    url = "http://www.xicidaili.com/nt/"
    _agent = [
        "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; AcooBrowser; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
        "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; Acoo Browser; SLCC1; .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506)",
        "Mozilla/4.0 (compatible; MSIE 7.0; AOL 9.5; AOLBuild 4337.35; Windows NT 5.1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
        "Mozilla/5.0 (Windows; U; MSIE 9.0; Windows NT 9.0; en-US)",
        "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)",
        "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)",
        "Mozilla/4.0 (compatible; MSIE 7.0b; Windows NT 5.2; .NET CLR 1.1.4322; .NET CLR 2.0.50727; InfoPath.2; .NET CLR 3.0.04506.30)",
        "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN) AppleWebKit/523.15 (KHTML, like Gecko, Safari/419.3) Arora/0.3 (Change: 287 c9dfb30)",
        "Mozilla/5.0 (X11; U; Linux; en-US) AppleWebKit/527+ (KHTML, like Gecko, Safari/419.3) Arora/0.6",
        "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.2pre) Gecko/20070215 K-Ninja/2.1.1",
        "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9) Gecko/20080705 Firefox/3.0 Kapiko/3.0",
        "Mozilla/5.0 (X11; Linux i686; U;) Gecko/20070322 Kazehakase/0.4.5",
        "Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.8) Gecko Fedora/1.9.0.8-1.fc10 Kazehakase/0.5.6",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_3) AppleWebKit/535.20 (KHTML, like Gecko) Chrome/19.0.1036.7 Safari/535.20",
        "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52",
    ]
    header = {'Connection': 'keep-alive',
              'Cache-Control': 'max-age=0',
              'Upgrade-Insecure-Requests': '1',
              'User-Agent': _agent[random.randint(0, len(_agent) - 1)],
              'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
              'Accept-Encoding': 'gzip, deflate, sdch',
              'Accept-Language': 'zh-CN,zh;q=0.8',
              }
    proxies = []

    def __init__(self):
        self.get_ip()
        print proxies.append(1)

    def get_ip(self):
        htmls = requests.get(self.url, headers=self.header, proxies=self.proxies).content
        # print re.findall(r"<td data-title=\"(.*?)\">(.*?)</td>", html.content)
        tree = etree.HTML(htmls)
        # proxy_list = tree.xpath('.//div[@id="freelist"]/table/tbody[@class="center"]//tr')
        # print proxy_list
        for i in tree.xpath('//tr')[1:]:
            ps = {}
            data = i.xpath('//td/text()')
            ps[data[5]] = data[5] + '://' + ':'.join(data[0:2])
            self.proxies.append(ps)
        return self.proxies

    def wx_get(self,x):
        wx_url = "http://weixin.sogou.com/weixin?type=2&s_from=input&query={}&ie=utf8&_sug_=n&_sug_type_=".format(x)
        html = requests.get(wx_url, headers=self.header, proxies=self.proxies[random.randint(0, len(self.proxies) - 1)]).content.replace('&amp;', '&')
        for i in re.findall("<a target=\"_blank\" href=\"(.*?)\"", html):
            global data_quere
            data_quere.put(i)


class Counter(threading.Thread):

    def __init__(self, lock, threadName, requests, url):
        '''@summary: 初始化对象。
           @param lock: 琐对象。
           @param threadName: 线程名称。
        '''
        print threadName+'lishao-start-run'
        super(Counter, self).__init__(name=threadName)
        self.lock = lock
        self.requests = requests
        self.url = url

    def _data_get(self):
        html = self.requests.get(self.url)
        req = etree.HTML(html.content)
        print "标题："+req.xpath("//title/text()")[0]
        print "作者："+req.xpath("//div[@id='meta_content']/a/text()")[0]

    def run(self):
        global count
        self.lock.acquire()
        self._data_get()
        self.lock.release()

lock = threading.Lock()
i = 0
if __name__ == '__main__':
    urls().wx_get("nvzhuang")

    while not data_quere.empty():
        Counter(lock, "thread-" + str(i), requests, data_quere.get()).start()
        i = i + 1
