# MISP Synchronisation

![MISP Synchronisation](https://github.com/MISP/misp-synchronisation/blob/main/docs/logo.png)

**MISP Synchronisation** is a project aimed at facilitating the local deployment of multiple instances of [MISP](https://www.misp-project.org/) and the configuration of their synchronisation connections.
It relies on the [MISP Docker](https://github.com/MISP/misp-docker) repository to easily deploy the different instances in containers.

In addition, this project includes a set of synchronisation tests based on the [PyMISP](https://github.com/MISP/PyMISP/) library to verify that data sharing between instances is working properly.

---

## 🚀 Main features

* **Automated deployment**: installation and configuration of ready-to-use MISP instances.
* **Containerization with Docker**: each instance is isolated and reproducible.
* **Automated testing**: Python scripts to validate inter-instance synchronisation.

---

## 📋 Prerequisites

Before installing and using this project, make sure your host system meets the following requirements:

* **Operating system**

  * [Ubuntu 22.04 LTS (Jammy Jellyfish)](https://releases.ubuntu.com/jammy/)
* **Containerization**

  * [Docker](https://docs.docker.com/get-docker/)


---

