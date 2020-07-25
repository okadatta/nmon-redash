FROM centos:centos8
RUN yum update
RUN yum install -y java-1.8.0-openjdk

RUN curl --create-dirs -o ~/.embulk/bin/embulk -L "http://dl.embulk.org/embulk-latest.jar"
RUN chmod +x ~/.embulk/bin/embulk
RUN echo 'export PATH="$HOME/.embulk/bin:$PATH"' >> ~/.bashrc
RUN . ~/.bashrc

RUN $HOME/.embulk/bin/embulk gem install embulk-parser-poi_excel
RUN $HOME/.embulk/bin/embulk gem install embulk-output-postgresql
RUN $HOME/.embulk/bin/embulk gem install embulk-filter-row

RUN curl -o ~/.digdag/bin/digdag --create-dirs -L "https://dl.digdag.io/digdag-latest"
RUN chmod +x ~/.digdag/bin/digdag
RUN echo 'export PATH="$HOME/.digdag/bin:$PATH"' >> ~/.bashrc

ENTRYPOINT ["/bin/bash"]
