FROM ubuntu:latest
RUN apt-get update && apt-get install -y \
    sudo \
    wget \
    vim

WORKDIR /opt
RUN wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh && \
    sh Anaconda3-2019.10-Linux-x86_64.sh -b -p /opt/anaconda3 && \
    rm -f Anaconda3-2019.10-Linux-x86_64.sh 
ENV PATH /opt/anaconda3/bin:$PATH

WORKDIR /
RUN pip install --upgrade pip

CMD ["jupyter", "lab", "--ip=0.0.0.0", "--allow-root", "--LabApp.token=''"]
# $ jupyter lab で起動
# --ip 0.0.0.0 ローカルホストの意味
# --allow-root ローカルで実行の為ルート権限（※ 別サーバーでならユーザー指定するべき）
# --LabApp.token トークン指定でセキュリティを高める。（ローカル上の為未指定）
