# XAIPlayground

### Setup NGINX
1. ```sudo apt-get install nginx```

2. ```cd /etc/nginx/sites-available```

3. ```sudo vim default```

4. Append 
```
server {
    listen 80;
    server_name {HOST_NAME};

    location / {
        proxy_pass http://{IP_ADDRESS}:8888;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
	proxy_set_header Origin "";
        proxy_read_timeout 86400;
    }

    location ~* /(api/kernels/[^/]+/(channels|iopub|shell|stdin)|terminals/websocket)/? {
        proxy_pass http://{IP_ADDRESS}:8888;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header Upgrade "websocket";
        proxy_set_header Connection "upgrade";
        proxy_set_header Origin "";
        proxy_read_timeout 86400;
    }
}
```

5. ```sudo service nginx configtest```

6. ```sudo service nginx restart```

### Run Docker container
1. Install Docker: https://docs.docker.com/install/linux/docker-ce/ubuntu/

2. ```docker pull continuumio/anaconda3```

3. ```docker run -i -t -d -p 8888:8888 --name jupyter continuumio/anaconda3 /bin/bash```

### Attach Docker container
1. ```docker attach jupyter```

2. ```/opt/conda/bin/conda install jupyter -y --quiet```

3. ```mkdir /opt/notebooks```

4. ```/opt/conda/bin/jupyter notebook --generate-config```

5. ```/opt/conda/bin/jupyter notebook password```

6. ```/opt/conda/bin/jupyter notebook --notebook-dir=/opt/notebooks --ip='0.0.0.0' --port=8888 --no-browser```

7. Detach: ```ctrl + P``` + ```ctrl + Q```

### Add Jupyter kernel
1. ```conda create -n ipykernel_py3 python=3.5.6 ipykernel ```

2. ```conda activate ipykernel_py3 ```

3. ```python -m ipykernel install --user --name ipykernel_py3 --display-name "Python (ipykernel_py3)"```

4. ```pip install -r requirements.txt```

5. ```conda deactivate```
