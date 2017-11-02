FROM mxnet/julia

ENV DEBIAN_FRONTEND=noninteractive
RUN useradd -m ubuntu
　
# Deps
RUN apt-get update -y && apt-get install -y sudo bzip2 wget htop libzmq3-dev build-essential gcc libnlopt0 curl cmake make nfs-common libgfortran3 unzip libhdf5-dev hdf5-tools git liblz4-dev openssh-server
　
# Anaconda with Jupyter
ENV CONDA="Miniconda3-latest-Linux-x86_64.sh"
RUN wget --quiet https://repo.continuum.io/miniconda/${CONDA} && /bin/bash /${CONDA} -b -p /miniconda3 && /miniconda3/bin/conda install -y jupyter
RUN chown -R ubuntu /miniconda3

# Julia
ENV JULIA="0.6/julia-0.6.1-linux-x86_64.tar.gz"
RUN mkdir /julia && curl -L https://julialang.s3.amazonaws.com/bin/linux/x64/${JULIA} \
    | tar zxC /julia --strip-components=1 && \
    sudo -u ubuntu -H PATH="/miniconda3/bin:$PATH" /julia/bin/julia -e 'Pkg.update(); for p in ["ZMQ","JSON","TimeZones","HDF5","IJulia","Distributions","SQLite","PyPlot","HypothesisTests","ParallelDataTransfer","AbstractTrees","Requests","Gumbo","OAuth","NLopt","PyCall","Nettle","ArgParse","ClusterManagers","JLD","Distances"]; Pkg.add(p); eval(Expr(:toplevel,Expr(:using,Symbol(p))));end'
　
RUN echo 'export PATH=/miniconda3/bin:$PATH' >> /home/ubuntu/.bashrc
RUN echo 'export PATH=/julia/bin:$PATH' >> /home/ubuntu/.bashrc

RUN chown -R ubuntu /home/ubuntu
　
RUN echo 'ubuntu ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

RUN echo 'export MXNET_HOME=/mxnet' >> /home/ubuntu/.bashrc
# Get the latest commit, the last version release doesn't work with Julia 0.6
RUN sudo -u ubuntu -H MXNET_HOME="/mxnet" /julia/bin/julia -e 'Pkg.checkout("MXNet"); using MXNet'

# Stupid annoying bug
RUN ln /dev/null /dev/raw1394


USER ubuntu
WORKDIR /home/ubuntu/
