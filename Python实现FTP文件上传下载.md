# Python实现FTP文件上传下载



```python
"""
FTP Util
"""

# !/usr/bin/python3
# coding: utf-8
import ftplib
import os
import socket
import time


class FTP(object):
    """
    connect to FTP server, download or upload file, disconnect
    """

    def __init__(self, host, port, user, pwd):
        self.host = host
        self.port = port
        self.user = user
        self.pwd = pwd

        self.client = None

    def connect(self):
        """
        connect by ftp server
        :return:
        """
        client = ftplib.FTP()
        client.connect(self.host, self.port, timeout=30)
        client.login(self.user, self.pwd)
        client.set_debuglevel(2)
        socket.setdefaulttimeout(1200)

        self.client = client

    def quit(self):
        """
        :return:
        """
        if self.client:
            time.sleep(1)
            self.client.quit()

    def isdir(self, remote):
        """
        remote path is file or folder
        :param remote: remote path
        :return:
        """
        try:
            self.client.cwd(remote)
            return True
        except:
            return False

    def listdir(self, remote):
        """
        list remote file paths by ftp server
        :param remote: remote folder path
        :return:
        """
        if not self.isdir(remote):
            return [remote]

        paths = self.client.nlst(remote)
        for i, p in enumerate(paths):
            paths[i] = p
        return paths

    def listree(self, remote):
        """
        list remote file paths by ftp server
        :param remote: remote folder path
        :return:
        """
        if not self.isdir(remote):
            return [remote]

        files = []
        paths = self.client.nlst(remote)
        for path in paths:
            if self.isdir(path):
                ps = self.listree(path)
                if ps:
                    files.extend(ps)
            else:
                files.append(path)
        return files

    def remove(self, remote):
        """
        delete remote file in ftp server
        :param remote: remote file path
        :return:
        """
        self.client.delete(remote)

    def store(self, remote, local):
        """
        download file from ftp server
        :param remote:
        :param local:
        :return:
        """
        writer = None
        try:
            name = os.path.basename(remote)
            path = os.path.join(local, name)

            folder = os.path.split(path)[0]
            if not os.path.exists(folder):
                os.makedirs(folder)

            writer = open(path, 'wb')
            self.client.retrbinary('RETR %s' % remote, writer.write, 1024)
            return path
        except Exception as err:
            raise err
        finally:
            if writer:
                writer.close()

```



```python
"""
SFTP Util
"""

# !/usr/bin/python3
# coding: utf-8
import os
import socket
import time
import traceback

import paramiko


class SFTP(object):
    """
    connect to SFTP server, download or upload file, disconnect
    """

    def __init__(self, host, port, user, pwd):
        self.host = host
        self.port = port
        self.user = user
        self.pwd = pwd

        self.transport = None
        self.client = None

    def connect(self):
        """
        connect by sftp server
        :return:
        """
        # way 1: It is ok to use only the default port
        # transport = paramiko.Transport((self.host, self.port))
        # transport.connect(username=self.user, password=self.pwd)

        # way 2: It's ok to use any port
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        ssh.connect(self.host, self.port, self.user, self.pwd, timeout=86400, banner_timeout=60)
        # shell = ssh.invoke_shell()
        # shell.keep_this = ssh

        transport = ssh.get_transport()
        client = paramiko.SFTPClient.from_transport(transport)
        socket.setdefaulttimeout(86400)

        self.transport = transport
        self.client = client

    def quit(self):
        """
        :return:
        """
        if self.client:
            time.sleep(1)
            self.client.close()
        if self.transport:
            time.sleep(1)
            self.transport.close()

    def isdir(self, remote):
        """
        remote path is file or folder
        :param remote: remote path
        :return:
        """
        if '' == remote:
            return True

        try:
            self.client.chdir(remote)
            return True
        except:
            return False

    def listdir(self, remote):
        """
        list remote file paths by sftp server
        :param remote: remote folder path
        :return:
        """
        if '' == remote:
            remote = '/'
        elif not self.isdir(remote):
            return [remote]

        paths = []
        names = self.client.listdir(remote)
        for i, name in enumerate(names):
            paths.append(os.path.join(remote, name))
        return paths

    def listree(self, remote):
        """
        list remote file paths by sftp server
        :param remote: remote folder path
        :return:
        """
        if '' == remote:
            remote = '/'
        elif not self.isdir(remote):
            return [remote]

        paths = []
        names = self.client.listdir(remote)
        for name in names:
            path = os.path.join(remote, name)
            if self.isdir(path):
                ps = self.listree(path)
                if ps:
                    paths.extend(ps)
            else:
                paths.append(path)
        return paths

    def remove(self, remote):
        """
        delete remote file in sftp server
        :param remote: remote file path
        :return:
        """
        try:
            self.client.remove(remote)
        except:
            traceback.print_exc()

    def store(self, remote, local):
        """
        download file from sftp server
        :param remote:
        :param local:
        :return:
        """
        try:
            name = os.path.basename(remote)
            path = os.path.join(local, name)

            folder = os.path.split(path)[0]
            if not os.path.exists(folder):
                os.makedirs(folder)

            self.client.get(remote, path)
            return path
        except Exception as err:
            raise err

```