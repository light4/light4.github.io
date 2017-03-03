[url](http://askubuntu.com/questions/144235/locale-variables-have-no-effect-in-remote-shell-perl-warning-setting-locale-f)

That's because your locale in your local machine is set to German, which SSH forwards to and tries to use on the server, but your server does not have it installed.

You've got several options:

Generate the locale. Generate the German locale on the server with sudo locale-gen de.
Stop forwarding locale from the client. Do not forward the locale environment variable from your local machine to the server. You can comment out the SendEnv LANG LC_* line in the local /etc/ssh/ssh_config file.
Stop accepting locale on the server. Do not accept the locale environment variable from your local machine to the server. You can comment out the AcceptEnv LANG LC_* line in the remote /etc/ssh/sshd_config file.
Set the server locale to English. Explicitly set the locale to English on the server. As an example, you can add the following lines to your remote ~/.bashrc or ~/.profile files:
export LANGUAGE="en"
export LANG="C"
export LC_MESSAGES="C"
If you don't have root access to the server, the Stop forwarding locale from the client option might be the best (and only) way to go.
