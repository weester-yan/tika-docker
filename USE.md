
## 构建镜像
docker pull --platform=linux/amd64 ubuntu:noble
docker build --no-cache --platform=linux/amd64 --build-arg="TIKA_VERSION=3.0.0" -t d.nextchat.dev:5000/apache-tika:3.0.0.0-full -f Dockerfile .


## 启动tika server服务
docker run -d -p 127.0.0.1:9998:9998 -v ./jars/:/tika-extras/ d.nextchat.dev:5000/apache-tika:3.0.0.0-full -c /tika-extras/tika-config.xml
docker exec -it -u root xxx bash
docker logs -f --tail 100 xxx

- OCR：jars目录下于OCR的JPEG相关的几个jar包，挂载到容器中，跟随启动命令加载
- 参数：tika-config.xml文件配置server启动配置，其中调用OCR服务默认超时5m，当前配置为10m


## 请求解析文档内容

- 调用/tika
curl 127.0.0.1:9998/tika -T '01项目常见问题分析及解决建议.doc' -H 'Accept: text/plain'

- form表单调用
curl 127.0.0.1:9998/tika/form -F 'upload=@01项目常见问题分析及解决建议.pdf' -H 'Accept: text/plain' -H 'X-Tika-OCRLanguage: eng+chi_sim'

> X-Tika-OCRLanguage：配置OCR模型识别语言，chi_sim表示简体中文，不配置无法识别中文

