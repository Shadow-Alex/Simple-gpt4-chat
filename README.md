# Simple-gpt4-chat

Just a simple gpt4 chat.



```python
import openai, os

# get host ip in wsl2.
def get_host_ip():
    import subprocess
    output = subprocess.check_output("cat /etc/resolv.conf | grep nameserver | awk '{ print $2 }'", shell=True)
    output = output.decode("utf-8").rstrip()
    return output


if __name__ == '__main__':
    # If you want to setup a proxy.
    host_ip = get_host_ip()
    openai.proxy = {
        'http': host_ip + ":7890",
        'https': host_ip + ":7890"
    }
    openai.api_key = "Your key here."

    # res = openai.Model.list() # A Useful API.

    while True: # chats
        print("\n**************** New chat ****************")
        chat_history = []
        while True: # chat.
            # Q:
            print("Q:", end='')
            new_chat = False
            user_input = ''
            while True:
                input_str = input()
                if input_str == 'EOS':
                    break
                if input_str == 'EOF':
                    new_chat = True
                    break
                else:
                    user_input += input_str

            if new_chat: # use EOF to signal a new chat!
                break

            chat_history.append({"role":"user", "content":user_input})

            completion = openai.ChatCompletion.create(
                model="gpt-4",
                messages=chat_history,
                stream=True,  # async call
                temperature=0,
            )

            ans = ''
            print("A:", end='')
            for item in completion:  # sync collect responses.
                try:
                    print(item['choices'][0]['delta']['content'], end='')
                    ans += item['choices'][0]['delta']['content']
                except:
                    pass  # "finish reason stop." or " assistance @ beginning."
            print('')

            chat_history.append({"role":"assistant", "content":ans})
```

![image](https://github.com/Shadow-Alex/Simple-gpt4-chat/assets/46802674/957685b1-3e4e-40e6-81c2-a54133c3999d)

