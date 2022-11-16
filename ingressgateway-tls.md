# 生成 CA 及证书密钥
https://istio.io/latest/zh/docs/tasks/traffic-management/egress/egress-gateway-tls-origination-sds/
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=example Inc./CN=example.com' -keyout example.com.key -out example.com.crt
openssl req -out httpbin.example.com.csr -newkey rsa:2048 -nodes -keyout httpbin.example.com.key -subj "/CN=httpbin.example.com/O=httpbin organization"
openssl x509 -req -sha256 -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 0 -in httpbin.example.com.csr -out httpbin.example.com.crt

kubectl create secret -n istio-system generic httpbin-credential --from-file=key=httpbin.example.com.key --from-file=cert=httpbin.example.com.crt

kubectl apply -f ingressgateway-tls.yaml

curl -v -HHost:httpbin.example.com --resolve httpbin.example.com:30097:192.168.2.181 --cacert /root/istio-1.15.3/example.com.crt https://httpbin.example.com:30097/status/418

curl -v -HHost:httpbin.example.com --resolve httpbin.example.com:30097:192.168.2.181 https://httpbin.example.com:30097/status/418
