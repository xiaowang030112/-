# -*- coding: utf-8 -*-
import requests
import sys
import json
from datetime import datetime, timedelta, timezone
import time
import hashlib

urls = {
    # 'login': 'https://xcx.xybsyw.com/login/login!wx.action',

    'login': 'https://xcx.xybsyw.com/login/login.action',
    'loadAccount': 'https://xcx.xybsyw.com/account/LoadAccountInfo.action',
    'ip': 'https://xcx.xybsyw.com/behavior/Duration!getIp.action',
    'trainId': 'https://xcx.xybsyw.com/student/clock/GetPlan!getDefault.action',
    # 'position':'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action',

    'sign': 'https://app.xybsyw.com/behavior/Duration.action',
    'autoSign': 'https://xcx.xybsyw.com/student/clock/Post!autoClock.action',
    'newSign': 'https://xcx.xybsyw.com/student/clock/PostNew!updateClock.action',
    'status': 'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action'
}


host1 = 'xcx.xybsyw.com'
host2 = 'app.xybsyw.com'


def getTimeStr():
    utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
    bj_dt = utc_dt.astimezone(timezone(timedelta(hours=8)))
    return bj_dt.strftime("%Y-%m-%d %H:%M:%S")


def str2md5(str):
    return hashlib.md5(str.encode(encoding='UTF-8')).hexdigest()


def log(content):
    print(getTimeStr() + ' ' + str(content))
    sys.stdout.flush()


# 获取Header

def getHeader(host):
    userAgent = 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36 MicroMessenger/7.0.9.501 NetType/WIFI MiniProgramEnv/Windows WindowsWechat'
    contentType = 'application/x-www-form-urlencoded'
    headers = {
        'user-agent': userAgent,
        'content-type': contentType,
        'host': host,
        'Connection': 'keep-alive'
    }
    return headers


# 获取账号和密码

def getuser(userInfo):
    data = {
        'username': userInfo['token']['username'],
        'password': str2md5(userInfo['token']['password'])
    }
    return data


# 登录获取sessionId和loginerId

def login(userInfo):
    data = getuser(userInfo)
    headers = getHeader(host1)
    url = urls['login']
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        ret = {
            'sessionId': resp['data']['sessionId'],
            'loginerId': resp['data']['loginerId']
        }
        log(f"sessionId:{resp['data']['sessionId']}")
        log(f"loginerId:{resp['data']['loginerId']}")
        return ret
    else:
        log(resp['msg'])
        exit(-1)


# 获取姓名

def getUsername(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['loadAccount']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['loginer']
        log(f"姓名:{ret}")
        return ret
    else:
        log('获取姓名失败')
        exit(-1)


# 获取ip

def getIP(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['ip']
    resp = requests.post(url=url, headers=headers).json()
    if('success' in resp['msg']):
        ret = resp['data']['ip']
        log(f'ip:{ret}')
        return ret
    else:
        log('ip获取失败')
        exit(-1)


# 获取trainID

def getTrainID(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['trainId']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['clockVo']['traineeId']
        log(f'traineeId:{ret}')
        return ret
    else:
        log('trainid获取失败')
        exit(-1)


# 获取经纬度\签到地址

def getPosition(sessionId, trainId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['status']
    data = {
        'traineeId': trainId
    }
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        address = resp['data']['postInfo']['address']
        lat = resp['data']['postInfo']['lat']
        lng = resp['data']['postInfo']['lng']
        ret = {
            'lat': lat,
            'lng': lng
        }
        log(f'经度:{lng}|纬度:{lat}')
        log(f'签到地址:{address}')
        return ret
    else:
        log('经纬度获取失败')
        exit(-1)


def getSignForm(data, user):
    timeStamp = int(time.time())
    form = {
        'login': '1',
        'appVersion': '1.5.75',
        'operatingSystemVersion': '10',
        'deviceModel': 'microsoft',
        'operatingSystem': 'android',
        'screenWidth': '415',
        'screenHeight': '692',
        'reportSrc': '2',
        'eventTime': timeStamp,
        'eventType': 'click',
        'eventName': 'clickSignEvent',
        'clientIP': data['ip'],
        'pageId': data['pageId'],  # 30

        'itemID': 'none',
        'itemType': '其他',
        'stayTime': 'none',
        'deviceToken': '',
        'netType': 'WIFI',
        'app': 'wx_student',
        'preferName': '成长',
        'pageName': '成长-签到',
        'userName': data['userName'],
        'userId': datimport requests
import sys
import json
from datetime import datetime, timedelta, timezone
import time
import hashlib

urls = {
    # 'login': 'https://xcx.xybsyw.com/login/login!wx.action',

    'login': 'https://xcx.xybsyw.com/login/login.action',
    'loadAccount': 'https://xcx.xybsyw.com/account/LoadAccountInfo.action',
    'ip': 'https://xcx.xybsyw.com/behavior/Duration!getIp.action',
    'trainId': 'https://xcx.xybsyw.com/student/clock/GetPlan!getDefault.action',
    # 'position':'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action',

    'sign': 'https://app.xybsyw.com/behavior/Duration.action',
    'autoSign': 'https://xcx.xybsyw.com/student/clock/Post!autoClock.action',
    'newSign': 'https://xcx.xybsyw.com/student/clock/PostNew!updateClock.action',
    'status': 'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action'
}


host1 = 'xcx.xybsyw.com'
host2 = 'app.xybsyw.com'


def getTimeStr():
    utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
    bj_dt = utc_dt.astimezone(timezone(timedelta(hours=8)))
    return bj_dt.strftime("%Y-%m-%d %H:%M:%S")


def str2md5(str):
    return hashlib.md5(str.encode(encoding='UTF-8')).hexdigest()


def log(content):
    print(getTimeStr() + ' ' + str(content))
    sys.stdout.flush()


# 获取Header

def getHeader(host):
    userAgent = 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36 MicroMessenger/7.0.9.501 NetType/WIFI MiniProgramEnv/Windows WindowsWechat'
    contentType = 'application/x-www-form-urlencoded'
    headers = {
        'user-agent': userAgent,
        'content-type': contentType,
        'host': host,
        'Connection': 'keep-alive'
    }
    return headers


# 获取账号和密码

def getuser(userInfo):
    data = {
        'username': userInfo['token']['username'],
        'password': str2md5(userInfo['token']['password'])
    }
    return data


# 登录获取sessionId和loginerId

def login(userInfo):
    data = getuser(userInfo)
    headers = getHeader(host1)
    url = urls['login']
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        ret = {
            'sessionId': resp['data']['sessionId'],
            'loginerId': resp['data']['loginerId']
        }
        log(f"sessionId:{resp['data']['sessionId']}")
        log(f"loginerId:{resp['data']['loginerId']}")
        return ret
    else:
        log(resp['msg'])
        exit(-1)


# 获取姓名

def getUsername(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['loadAccount']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['loginer']
        log(f"姓名:{ret}")
        return ret
    else:
        log('获取姓名失败')
        exit(-1)


# 获取ip

def getIP(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['ip']
    resp = requests.post(url=url, headers=headers).json()
    if('success' in resp['msg']):
        ret = resp['data']['ip']
        log(f'ip:{ret}')
        return ret
    else:
        log('ip获取失败')
        exit(-1)


# 获取trainID

def getTrainID(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['trainId']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['clockVo']['traineeId']
        log(f'traineeId:{ret}')
        return ret
    else:
        log('trainid获取失败')
        exit(-1)


# 获取经纬度\签到地址

def getPosition(sessionId, trainId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['status']
    data = {
        'traineeId': trainId
    }
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        address = resp['data']['postInfo']['address']
        lat = resp['data']['postInfo']['lat']
        lng = resp['data']['postInfo']['lng']
        ret = {
            'lat': lat,
            'lng': lng
        }
        log(f'经度:{lng}|纬度:{lat}')
        log(f'签到地址:{address}')
        return ret
    else:
        log('经纬度获取失败')
        exit(-1)


def getSignForm(data, user):
    timeStamp = int(time.time())
    form = {
        'login': '1',
        'appVersion': '1.5.75',
        'operatingSystemVersion': '10',
        'deviceModel': 'microsoft',
        'operatingSystem': 'android',
        'screenWidth': '415',
        'screenHeight': '692',
        'reportSrc': '2',
        'eventTime': timeStamp,
        'eventType': 'click',
        'eventName': 'clickSignEvent',
        'clientIP': data['ip'],
        'pageId': data['pageId'],  # 30

        'itemID': 'none',
        'itemType': '其他',
        'stayTime': 'none',
        'deviceToken': '',
        'netType': 'WIFI',
        'app': 'wx_student',
        'preferName': '成长',
        'pageName': '成长-签到',
        'userName': data['userName'],
        'userId': datimport requests
import sys
import json
from datetime import datetime, timedelta, timezone
import time
import hashlib

urls = {
    # 'login': 'https://xcx.xybsyw.com/login/login!wx.action',

    'login': 'https://xcx.xybsyw.com/login/login.action',
    'loadAccount': 'https://xcx.xybsyw.com/account/LoadAccountInfo.action',
    'ip': 'https://xcx.xybsyw.com/behavior/Duration!getIp.action',
    'trainId': 'https://xcx.xybsyw.com/student/clock/GetPlan!getDefault.action',
    # 'position':'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action',

    'sign': 'https://app.xybsyw.com/behavior/Duration.action',
    'autoSign': 'https://xcx.xybsyw.com/student/clock/Post!autoClock.action',
    'newSign': 'https://xcx.xybsyw.com/student/clock/PostNew!updateClock.action',
    'status': 'https://xcx.xybsyw.com/student/clock/GetPlan!detail.action'
}


host1 = 'xcx.xybsyw.com'
host2 = 'app.xybsyw.com'


def getTimeStr():
    utc_dt = datetime.utcnow().replace(tzinfo=timezone.utc)
    bj_dt = utc_dt.astimezone(timezone(timedelta(hours=8)))
    return bj_dt.strftime("%Y-%m-%d %H:%M:%S")


def str2md5(str):
    return hashlib.md5(str.encode(encoding='UTF-8')).hexdigest()


def log(content):
    print(getTimeStr() + ' ' + str(content))
    sys.stdout.flush()


# 获取Header

def getHeader(host):
    userAgent = 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36 MicroMessenger/7.0.9.501 NetType/WIFI MiniProgramEnv/Windows WindowsWechat'
    contentType = 'application/x-www-form-urlencoded'
    headers = {
        'user-agent': userAgent,
        'content-type': contentType,
        'host': host,
        'Connection': 'keep-alive'
    }
    return headers


# 获取账号和密码

def getuser(userInfo):
    data = {
        'username': userInfo['token']['username'],
        'password': str2md5(userInfo['token']['password'])
    }
    return data


# 登录获取sessionId和loginerId

def login(userInfo):
    data = getuser(userInfo)
    headers = getHeader(host1)
    url = urls['login']
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        ret = {
            'sessionId': resp['data']['sessionId'],
            'loginerId': resp['data']['loginerId']
        }
        log(f"sessionId:{resp['data']['sessionId']}")
        log(f"loginerId:{resp['data']['loginerId']}")
        return ret
    else:
        log(resp['msg'])
        exit(-1)


# 获取姓名

def getUsername(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['loadAccount']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['loginer']
        log(f"姓名:{ret}")
        return ret
    else:
        log('获取姓名失败')
        exit(-1)


# 获取ip

def getIP(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['ip']
    resp = requests.post(url=url, headers=headers).json()
    if('success' in resp['msg']):
        ret = resp['data']['ip']
        log(f'ip:{ret}')
        return ret
    else:
        log('ip获取失败')
        exit(-1)


# 获取trainID

def getTrainID(sessionId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['trainId']
    resp = requests.post(url=url, headers=headers).json()
    if('成功' in resp['msg']):
        ret = resp['data']['clockVo']['traineeId']
        log(f'traineeId:{ret}')
        return ret
    else:
        log('trainid获取失败')
        exit(-1)


# 获取经纬度\签到地址

def getPosition(sessionId, trainId):
    headers = getHeader(host1)
    headers['cookie'] = f'JSESSIONID={sessionId}'
    url = urls['status']
    data = {
        'traineeId': trainId
    }
    resp = requests.post(url=url, headers=headers, data=data).json()
    if('成功' in resp['msg']):
        address = resp['data']['postInfo']['address']
        lat = resp['data']['postInfo']['lat']
        lng = resp['data']['postInfo']['lng']
        ret = {
            'lat': lat,
            'lng': lng
        }
        log(f'经度:{lng}|纬度:{lat}')
        log(f'签到地址:{address}')
        return ret
    else:
        log('经纬度获取失败')
        exit(-1)


def getSignForm(data, user):
    timeStamp = int(time.time())
    form = {
        'login': '1',
        'appVersion': '1.5.75',
        'operatingSystemVersion': '10',
        'deviceModel': 'microsoft',
        'operatingSystem': 'android',
        'screenWidth': '415',
        'screenHeight': '692',
        'reportSrc': '2',
        'eventTime': timeStamp,
        'eventType': 'click',
        'eventName': 'clickSignEvent',
        'clientIP': data['ip'],
        'pageId': data['pageId'],  # 30

        'itemID': 'none',
        'itemType': '其他',
        'stayTime': 'none',
        'deviceToken': '',
        'netType': 'WIFI',
        'app': 'wx_student',
        'preferName': '成长',
        'pageName': '成长-签到',
        'userName': data['userName'],
        'userId': dat
