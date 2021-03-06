FROM cyberhubs/corehub

MAINTAINER Falk Herwig

ENV DEBIAN_FRONTEND noninteractive

EXPOSE 8888
 
USER root
WORKDIR /root
ENV HOME /root

COPY ./apt_packages_mlhub.txt /tmp/apt_packages_mlhub.txt

RUN apt-get update && \
    apt-get -y install $(cat /tmp/apt_packages_mlhub.txt) && \
    apt-get autoremove --yes && apt-get clean all && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ADD ./single_pip2_requirements_mlhub.txt /tmp/requirements/single_pip2_requirements_mlhub.txt
ADD ./single_pip3_requirements_mlhub.txt /tmp/requirements/single_pip3_requirements_mlhub.txt

RUN pip2 install -r /tmp/requirements/single_pip2_requirements_mlhub.txt
RUN pip3 install -r /tmp/requirements/single_pip3_requirements_mlhub.txt

RUN python2 -m pip install ipykernel && python2 -m ipykernel install && python2 -m bash_kernel.install && \
    python3 -m pip install ipykernel && python3 -m ipykernel install && python3 -m bash_kernel.install

# Add extensions that include the initialization cells and many others...

COPY jupyterhub-singleuser /usr/local/bin/jupyterhub-singleuser

COPY singleuser.sh /srv/singleuser/singleuser.sh

COPY README_MLhub.ipynb /home/user/notebooks

#RUN jupyter contrib nbextension install --system && \
#    jupyter nbextension enable --py --sys-prefix widgetsnbextension 

#RUN jupyter nbextension enable codefolding/main && \
#    jupyter nbextension enable init_cell/main

# Jupyter lab section

#RUN jupyter serverextension enable --py jupyterlab --sys-prefix &&\
#    jupyter labextension install @jupyterlab/hub-extension

# Add trusted notebooks and init_cells

COPY  secret/nbsignatures.db /home/user/.local/share/jupyter/nbsignatures.db
COPY  secret/notebook_secret /home/user/.local/share/jupyter/notebook_secret
COPY  nbconfig/notebook.json /usr/local/etc/jupyter/nbconfig/notebook.json
COPY  custom /home/user/.jupyter/custom

RUN chmod -R 775 /home/user/notebooks

RUN chown -R user:user /home/user && \
    chmod -R 775 /home/user/ && \
    chmod 775 /usr/local/bin/jupyterhub-singleuser && \
    chmod 775 /srv/singleuser/singleuser.sh

COPY ./trusted_nb.sh /srv/singleuser/trusted_nb.sh
RUN chmod +x /srv/singleuser/trusted_nb.sh

USER user
ENV HOME /home/user
WORKDIR /home/user

COPY ipython_config.py /etc/ipython/ipython_config.py
CMD ["/bin/bash", "/srv/singleuser/singleuser.sh"]

RUN /srv/singleuser/trusted_nb.sh

ENV SYGMADIR /opt/NuPyCEE