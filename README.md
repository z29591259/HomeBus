# HomeBus

Show the inter city bus estimated time of arrival near my home.

Data sourece: [PTX (Public Transport Data eXchange)](http://ptx.transportdata.tw/PTX)

Use native javascript ajax to get json data from PTX Web API, and show different time unit depend on the seconds.

```javascript
function getPtx(url, success, failed, timeout, onTimeout){
	let xhr = new XMLHttpRequest();			
	xhr.open('GET', url, true);
	let header = GetAuthorizationHeader();
	xhr.setRequestHeader('Authorization', header['Authorization']);
	xhr.setRequestHeader('X-Date', header['X-Date']);
	if(timeout != undefined && onTimeout != undefined){
		xhr.timeout = timeout; // time in milliseconds
		xhr.ontimeout = function (e) {
		  (onTimeout && typeof(onTimeout) === "function") && onTimeout(xhr.responseText);
		};
	}else{
		xhr.timeout = 10000; // time in milliseconds
	}
	xhr.onload = function() {
	    if (xhr.status === 200) {
	        (success && typeof(success) === "function") && success(xhr.responseText);
	    }
	    else {
	        (failed && typeof(failed) === "function") && failed();
	    }
	};
	xhr.send();
}
function GetAuthorizationHeader() {
    //this is test key
    let AppID = 'FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF';
    let AppKey = 'FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF';

    let GMTString = new Date().toGMTString();
    let ShaObj = new jsSHA('SHA-1', 'TEXT');
    ShaObj.setHMACKey(AppKey, 'TEXT');
    ShaObj.update('x-date: ' + GMTString);
    let HMAC = ShaObj.getHMAC('B64');
    let Authorization = 'hmac username=\"' + AppID + '\", algorithm=\"hmac-sha1\", headers=\"x-date\", signature=\"' + HMAC + '\"';

    return { 'Authorization': Authorization, 'X-Date': GMTString /*,'Accept-Encoding': 'gzip'*/}; //如果要將js運行在伺服器，可額外加入 'Accept-Encoding': 'gzip'，要求壓縮以減少網路傳輸資料量
}
```

Test API online from [here](http://ptx.transportdata.tw/MOTC/Swagger/#!/InterCityBusApi/InterCityBusApi_Route_0).

Other language sample code: [https://github.com/ptxmotc/Sample-code](https://github.com/ptxmotc/Sample-code)