## ディレクトリ構成

```
root
├- README.md  このファイル
├- HowToGenerate.md  このファイル
├- doc 
|   ├- gRPC
|	|    └- index.html         gRPC API仕様書
|	└- swagger
|	     ├- rest_sdsp_g.html   REST SDSP_G API仕様書
|	     └- rest_sdsp_a.html   REST SDSP_A API仕様書
|
├- proto  gRPC protoファイル
```

## GO gRPCコード生成方法

- 　リポジトリディレクトリ/srcで下記を実行
-   protoc --go_out=go/. --go-grpc_out=go/. airmobility/*.proto


## gRPC API仕様書生成方法

protoファイルからgRPC htmlファイルを生成する

### 環境条件

dockerコンテナでprotoc-gen-docを利用する。

https://hub.docker.com/r/pseudomuto/protoc-gen-doc


### ドキュメント生成手順

このファイル（HowToGenerate.md）が存在するディレクトリでprotoc-gen-docを実行する。

```
$ ls
HowToGenerate.md        README.md               README2.md              docs                    images                  memo.md                 src

$ docker run --rm -v $(pwd)/docs/gRPC:/out -v $(pwd)/src:/protos pseudomuto/protoc-gen-doc --doc_opt=html,index.html generic/sdsp_g.proto airmobility/sdsp_a.proto airmobility/object_a.proto
```

## OPEN APIドキュメント生成方法

protoファイルからREST API htmlファイルを生成する

### 環境構築
config/service.yamlにgRPCのAPIとRESTのパスの対応付けを定義する

### swaggerを出力する
このファイル（HowToGenerate.md）が存在するディレクトリでprotocによってopenapi(swagger.json)を出力する

grpc_api_configurationでconfig/service.yamlを指定する


```
$ ls
HowToGenerate.md        README.md               README2.md              docs                    images                  memo.md                 src

$protoc -I./src/airmobility -I./src/ --openapiv2_out=./src/airmobility --openapiv2_opt grpc_api_configuration=src/config/service.yaml sdsp_a.proto

$protoc -I./src/airmobility -I./src/ --openapiv2_out=./src/airmobility --openapiv2_opt grpc_api_configuration=src/config/service.yaml object_a.proto

$protoc -I./src/generic --openapiv2_out=./src/generic --openapiv2_opt grpc_api_configuration=src/config/service.yaml sdsp_g.proto
```


## htmlに変換する
openapi(swagger.json)をredoc-cliを出力する

```
$redoc-cli bundle src/airmobility/sdsp_a.swagger.json -o docs/rest/rest_sdsp_a.html
$redoc-cli bundle src/generic/sdsp_g.swagger.json -o docs/rest/rest_sdsp_g.html
```
