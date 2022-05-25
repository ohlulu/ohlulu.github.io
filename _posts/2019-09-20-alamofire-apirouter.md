---
title: "Alamofire - APIRouter"
date: "2019-09-20"
categories: 
  - "technology"
tags: 
  - "alamofire"
  - "tip"
img_path: /assets/img/post/
coverImage: "router.jpeg"
---

# Alamofire - APIRouter

## 2020/6 更新

在 iPlayground2019 聽完王巍大大的 talk 之後，又自己實作了三次，已經放棄下面的封裝方式 範例在 [Github](https://github.com/ohlulu/NetworkDemo)，因為實作概念基本上等同王巍大大的 Talk（多了一些參考自 Moya 的概念）所以等 哪天心情好 有空，再來寫一篇文章好了哈哈哈哈哈。

### \# 起

[Alamofire](https://github.com/Alamofire/Alamofire) 相信開發 iOS 的人都不陌生，就算沒用過也聽，非常好用的一個第三方網路處理套件，網路上也有非常多關於 Alamofire 的封裝，google alamofire router 的教學文章也是一大堆。

大部分的文章看起來都會像這樣：

```swift
enum APIRouter {

    case fetchData
    case uploadData
    case deleteData(String)
    var method: HTTPMethod {
        switch self {
        case .fetchData: return .get
        case .uploadData: return .post
        case .deleteData: return .post
        }
    }

    var path: String {
        switch self {
        case .fetchData: return "/data/list"
        case .uploadData: return "/data/uplod"
        case .deleteData: return "/data/delete"
        }
    }

    var parameters: Parameters? {
        switch self {
        case .fetchData:
            return ["time": DataLastSyncTime]
        case .uploadData:
            return ["data": Data]
        case .deleteData(let id):
            return ["id": id]
        }
    }

    func asURLRequest() throws -> URLRequest {
        //...
        retutn ...
    }
}
```

### \# 承

假設今天我們是的 APP 只有需要呼叫 `十幾支 API` ，這樣的寫法或許感覺不到什麼問題，但是當 API 的數量增加到 `幾十支` 的時候，就會發現整個 router 非常的混亂，每次增加一支 API ，就需要把整個檔案重頭滾動到尾，一個 http request 的 URL、http method、 parameters，散落在檔案的各處，不管是新增還是修改都極為麻煩。

### \# 轉

後來在網路上發現了一個更好的寫法，但原文找不到了，後來我憑著印象重現了當初看到的文章。

```swift
struct APIRouter: URLRequestConvertible {

    let path: String
    let method: HTTPMethod
    var parameters: Parameters?


    func asURLRequest() throws -> URLRequest {

        let url = URL(string: "https://www.google.com")!
            .appendingPathComponent(path)

        var urlRequest = URLRequest(url: url)

        urlRequest.httpMethod = method.rawValue

        urlRequest.timeoutInterval = 30

        // Http common header
        urlRequest.allHTTPHeaderFields = ESHTTPHeaders.default

        // add custom

        do {
            return try JSONEncoding.default.encode(urlRequest, with: parameters)
        } catch {
            fatalError("\(error)")
        }
    }
}

extension APIRouter {
    static func login(requestModel model: LoginRequestModel) -> APIRouter {
        return APIRouter(path: "/login",
                         method: .post,
                         parameters: model.convertToParameter())
    }
}
```

這邊應該是使用了 `工廠模式(factory pattern)`

我把 enum 改成了 struct ，然後一樣 confirm `URLRequestConvertible` ，並實作 asURLRequest() ，然後使用 static func create 這個 struct。

其中最後的 `model.convertToParameter()` 是自己宣告 & 實作的 protocol

```swift
protocol ParameterConvertible {
    func convertToParameter() -> Parameters?
}

extension ParameterConvertible where Self: Encodable {

    func convertToParameter() -> Parameters? {
        do {
            let data = try JSONEncoder().encode(self)
            return try JSONSerialization.jsonObject(with: data, options: []) as? [String: Any]
        } catch {
            fatalError("\(error)")
        }
    }
}
```

假設 model 沒有特殊的變化，就可以使用預設的 `func convertToParameter()` ，如果今天 model 與 api 的參數對應不起來，也可以 model 自己實作這個 func 來客製參數的結構。

### \# 合

所以我們把一個 http request 的 URL、http method、 parameters... 的資訊，通通寫在一個 func 了，是不是清楚許多Ｒ。
