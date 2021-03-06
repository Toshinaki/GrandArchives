Deploy Django to Cloud
===========================

Deploy to GCP
---------------

GAE Stanadrd
~~~~~~~~~~~~~~

1. static files

    1. 创建一个 GCS bucket, 运行 `gsutil defacl set public-read gs://${bucketname}` 设置其权限为 public
    2. 运行 `python manage.py collectstatic`, 将静态文件收集到 `STATIC_ROOT` 所指向的文件夹中
    3. 将本地静态文件夹同步至 bucket: `gsutil -m rsync -r ./${Local_Static_Folder}/ gs://${bucketname}/static`

        - `-m`: perform parallel (multi-threaded/multi-processing) synchronization
        - `-r`: copy only new/changed files without deleting extra files

2. SQL Database

    1. 连接至 SQL 服务器
    2. 检查 models 更新, 创建迁移文件: `python manage.py makemigrations`
    3. 执行迁移: `python manage.py migrate`
    4. 创建超级用户 `python manage.py createsuperuser`

3. `settings.py`

    1. 将 domain 添加至 `ALLOWED_HOSTS`
    2. 设置环境变量 (environment varibales 或者使用 Datastore), 以保证各种密钥能够正确加载
    3. 设置静态文件的伺服地址 `STATIC_URL`; 使用 GCS bucket 时, 可以设置为 `https://storage.googleapis.com/${bucketname}/static/`
    4. 设置 `DEBUG` 为 `False` (optional; recommended)

4. deploy

    - 运行 `gcloud app deploy`