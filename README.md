# Docker

Docker ini digunakan untuk ci / cd (gitlab) untuk membuat apk ionic dan react native.
Repo ini terdiri dari 3 docker image.

1. [base_hybrid](https://github.com/imron02/Docker/tree/master/base_hybrid). Image ini sebagai base docker dari docker ionic dan react native.
2. [ionic](https://github.com/imron02/Docker/tree/master/ionic). Image ini digunakan untuk membuild aplikasi ionic. Baik aplikasi web, aplikasi android (dev) dan aplikasi android (prod).
3. [react_native](https://github.com/imron02/Docker/tree/master/react_native). Image ini digunakan untuk membuild aplikasi react native. Baik aplikasi android (dev) dan aplikasi android (prod).
4. Cara penggunakan atau membuat _docker_ ini, silahkan baca wikinya [di sini](https://github.com/imron02/Docker/wiki).

> Jika ingin ikut berkontribusi, silahkan fork repo ini.
> Dockerfile ini berdasarkan dari docker [node:8.11.2](https://hub.docker.com/_/node/), menggunakan sistem operasi debian jessie