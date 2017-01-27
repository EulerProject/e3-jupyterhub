FROM jupyter/minimal-notebook

MAINTAINER Thomas Rodenhausen <thomas.rodenhausen@gmail.com>

USER root

# Install all OS dependencies for fully functional notebook server
RUN apt-get update && apt-get install -yq --no-install-recommends \
    graphviz \
    python-pip \
    pkg-config \
    libfreetype6-dev \
    python-numpy \
    sed \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install python modules
RUN pip2 install \
    pyyaml \ 
    docopt \
    pinject \
    autologging \
    matplotlib \
    networkx \
    gitpython

RUN git config --global user.email "e3"
RUN git config --global user.name "e3"

# Clone euler repos
RUN git clone https://github.com/EulerProject/EulerX /opt/eulerx
#RUN sed -i '1s|.*|#!/usr/bin/env python2|' /opt/eulerx/src-el/euler2
#RUN sed -i '1s|.*|#!/usr/bin/env python2|' /opt/eulerx/src-el/y2d
#RUN grep -rl '#!/usr/bin/env python' /opt/eulerx/src-el/ | xargs sed -i 's|#!/usr/bin/env python|#!/usr/bin/env python2|g'
#RUN grep -rl '#! /usr/bin/env python' /opt/eulerx/src-el/ | xargs sed -i 's|#! /usr/bin/env python|#!/usr/bin/env python2|g'
RUN chown -R jovyan:root /opt/eulerx/default_stylesheet
RUN git clone https://github.com/EulerProject/e3 /opt/e3
RUN git clone https://github.com/EulerProject/e3-kernel /opt/e3-kernel
RUN git clone https://github.com/EulerProject/euler2-demos /opt/euler2-demos

# Install e3 jupyter kernel
ENV E3KERNEL_CONTENT='{"argv": ["/opt/conda/bin/python", "/opt/e3-kernel/e3_kernel.py", "{connection_file}", "/home/jovyan/work"], "display_name": "e3", "language": "e3"}'
RUN mkdir -p /opt/conda/share/jupyter/kernels/e3-kernel/
RUN echo $E3KERNEL_CONTENT > /opt/conda/share/jupyter/kernels/e3-kernel/kernel.json

# Install euler prerequisits 
RUN wget http://www.dlvsystem.com/files/dlv.x86-64-linux-elf-static.bin -P /opt/reasoners/dlv
RUN wget -O /opt/reasoners/gringo-4.5.4-linux-x86_64.tar.gz "https://downloads.sourceforge.net/project/potassco/gringo/4.5.4/gringo-4.5.4-linux-x86_64.tar.gz?r=https%3A%2F%2Fsourceforge.net%2Fprojects%2Fpotassco%2Ffiles%2Fgringo%2F4.5.4%2F&ts=1484341313&use_mirror=master"
RUN wget -O /opt/reasoners/claspD-1.1.4-x86-linux.tar.gz "http://downloads.sourceforge.net/project/potassco/claspD/1.1.4/claspD-1.1.4-x86-linux.tar.gz?r=https%3A%2F%2Fsourceforge.net%2Fprojects%2Fpotassco%2Ffiles%2FclaspD%2F1.1.4%2F&ts=1483579150&use_mirror=master"
RUN wget http://www.cs.unm.edu/%7Emccune/mace4/download/LADR-2009-11A.tar.gz -P /opt/reasoners
RUN tar zxvf /opt/reasoners/gringo-4.5.4-linux-x86_64.tar.gz -C /opt/reasoners
RUN rm -R /opt/reasoners/gringo-4.5.4-linux-x86_64.tar.gz
RUN tar zxvf /opt/reasoners/claspD-1.1.4-x86-linux.tar.gz -C /opt/reasoners/
RUN rm -R /opt/reasoners/claspD-1.1.4-x86-linux.tar.gz
RUN tar zxvf /opt/reasoners/LADR-2009-11A.tar.gz -C /opt/reasoners
RUN rm /opt/reasoners/LADR-2009-11A.tar.gz
RUN make all -C /opt/reasoners/LADR-2009-11A
RUN ln -s /opt/reasoners/dlv/dlv.x86-64-linux-elf-static.bin /usr/local/bin/dlv
RUN ln -s /opt/reasoners/gringo-4.5.4-linux-x86_64/gringo /usr/local/bin/gringo
RUN ln -s /opt/reasoners/claspD-1.1.4/claspD-1.1.4 /usr/local/bin/claspD
RUN ln -s /opt/reasoners/LADR-2009-11A/bin/mace4 /usr/local/bin/mace4
RUN ln -s /opt/reasoners/LADR-2009-11A/bin/prover9 /usr/local/bin/prover9
RUN chmod +x /usr/local/bin/dlv

# Add binaries
RUN ln -s /opt/eulerx/src-el/euler2 /usr/local/bin/euler2
RUN ln -s /opt/eulerx/src-el/y2d /usr/local/bin/y2d
RUN ln -s /opt/eulerx/src-el/euler /usr/local/bin/euler
RUN ln -s /opt/e3/e3 /usr/local/bin/e3
ENV PYTHONPATH /opt/e3:$PYTHONPATH

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_USE
