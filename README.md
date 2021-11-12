# WebAutoDocsSite       
     
## Deployment - Manual

開発中の任意のコードをデプロイする方法について記述する。

### Steps

#### 0. 更新zipファイルが存在しない場合は準備

clone && cd

```shell
git clone https://github.com/Widen/cloudfront-auth.git

cd cloudfront-auth
```

make zipfile
```shell
./build.sh

Enter distribution name: cloudfront-auth
Authentication methods:
    (1) Google
    (2) Microsoft
    (3) GitHub
    (4) OKTA
    (5) Auth0
    (6) Centrify
    (7) OKTA Native
    Select an authentication method:  1


>>: Client ID:  ${Client ID}
>>: Client Secret:  ${Client Secret}
>>: Redirect URI:  ${Redirect URI}
>>: Hosted Domain:  @tier4.jp
>>: Session Duration (hours):  (0) 1
>>: Authorization methods:
   (1) Hosted Domain - verify email's domain matches that of the given hosted domain
   (2) HTTP Email Lookup - verify email exists in JSON array located at given HTTP endpoint
   (3) Google Groups Lookup - verify email exists in one of given Google Groups

   Select an authorization method:  1
Done... created Lambda function distributions/cloudfront-auth/cloudfront-auth.zip
```

unzip

```shell
unzip distributions/cloudfront-auth/cloudfront-auth.zip -d  tmp-`date '+%Y%m%d'`  && cd tmp-`date '+%Y%m%d'`
```

fix index.js(line 58)


```shell
-- befor --
  const request = event.Records[0].cf.request;
  const headers = request.headers;
  const queryDict = qs.parse(request.querystring);
```

```shell
-- after --
 const request = event.Records[0].cf.request;  

  // ADD START
  const olduri = request.uri;
  
  // 末尾が/で終わっている場合はindex.htmlを付与
  const newuri = olduri.replace(/\/$/, '\/index.html');

  // リクエストのURLを差し替える
  request.uri = newuri;
  // ADD END

  const headers = request.headers;
  const queryDict = qs.parse(request.querystring);

```

zip  
※xxxはenv(dev stg prd)

```shell
zip -r cloudfront-auth-xxx.zip *
```
zipファイルをMakefileと同一フォルダへ配置



#### 1. Deploy

Execute the following command.

以下のコマンドを実行する。

```shell
env AWS_PROFILE=xxx make deploy-dev env=xxx
```
