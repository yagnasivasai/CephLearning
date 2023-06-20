## 



    aws s3 ls s3://cephsync --endpoint-url http://192.168.159.129:8000
    aws s3 cp s3://cephsync/* --endpoint-url http://192.168.159.129:8000
    aws s3 cp s3://cephsync/* * --endpoint-url http://192.168.159.129:8000
    aws s3 sync s3://credit-nirvana/ --endpoint-url http://192.168.159.129:8000
    aws s3 ls --summarize --human-readable --recursive s3://cephsync --endpoint-url http://192.168.159.129:8000
