# aws_iam_localsetup
you create new User added appropriate permission in Aws IAM.

you create OS user *aws_console*. *aws_console* is writtent by using you create new user.

And, you aws cli was install and every user is enable to use aws cli.
## mac OS
```
brew install expect

yousetupuser=[yousetupuser]
su - $yousetupuser -c 'pip3 install pexpect --user'
# Update login shell for macOS.
su - $yousetupuser -c "chsh -s /bin/bash"

# Firstly, for sake of simplicity, you set Environment aws accesskey, aws password, aws secrect accesskey, and etc.
# AWS_PASSWORD and AWS_CONSOLE_LOGIN_URL are optionnal.These are setten to be easy ordinary time.
cat << END >> aws_credential.py
import csv
import os
with open(os.path.join(os.environ["HOME"],'new_user_credentials.csv')) as f:
    data = csv.DictReader(f)
    for row in data:
        with open(os.path.join(os.environ["HOME"],'.bash_profile'),'a') as bash_profile:
            print('export AWS_ACCESS_KEY={}'.format(row['Access key ID']),bash_profile)
            print('export AWS_SECRET_ACCESS_KEY={}'.format(row['Secret access key']),bash_profile)
            print('export AWS_PASSWORD={}'.format(row['Password']),bash_profile)
            print('export AWS_CONSOLE_LOGIN_URL={}'.format(row['Console login link']),bash_profile)
            print('export AWS_DEFAULT_REGION={}'.format('ap-northeast-1'),bash_profile)

END

cat << END >> aws_credential.py
import csv
import os
import pexpect
with open(os.path.join(os.environ["HOME"],'new_user_credentials.csv')) as f:
    data = csv.DictReader(f)
    for row in data:
        prc = pexpect.spawn("ufw enable")
        prc.expect("AWS Access Key ID [None]:")
        prc.sendline(row['Access key ID'])
        prc.expect("AWS Secret Access Key [None]:")
        prc.sendline(row['Secret access key'])
        prc.expect("Default region name [None]:")
        prc.sendline('ap-northeast-1')
        prc.expect("Default output format [None]:")
        prc.sendline('json')
        prc.expect( pexpect.EOF )
        
        with open(os.path.join(os.environ["HOME"],'.bash_profile'),'a') as bash_profile:
            print('export AWS_PASSWORD={}'.format(row['Password']),bash_profile)
            print('export AWS_CONSOLE_LOGIN_URL={}'.format(row['Console login link']),bash_profile)

END

sudo mv aws_credential.py new_user_credentials.csv /Users/$yousetupuser
sudo chown $yousetupuser /Users/$yousetupuser/aws_credential.py
sudo chown $yousetupuser /Users/$yousetupuser/new_user_credentials.csv

# execute python script and set aws credential.
su - $yousetupuser "python3 aws_credential.py"
```
