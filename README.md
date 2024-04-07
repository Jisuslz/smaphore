Para Ansible, hay una variedad de scripts y playbooks que pueden ser poderosos y útiles para aplicar a servidores Ubuntu, dependiendo de tus necesidades específicas. Aquí hay algunos ejemplos:

1. **Actualización del Sistema y Paquetes**: Un script básico para asegurarse de que el sistema y los paquetes estén actualizados:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Update apt package cache
          apt:
            update_cache: yes

        - name: Upgrade all packages
          apt:
            upgrade: yes
            update_cache: yes
    ```

2. **Instalación de Paquetes**: Para instalar paquetes específicos en los servidores Ubuntu:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Install required packages
          apt:
            name: 
              - package1
              - package2
              - package3
            state: present
    ```

3. **Configuración de Firewall con UFW**: Para configurar el Firewall Uncomplicated Firewall (UFW) en Ubuntu:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Allow SSH through firewall
          ufw:
            rule: allow
            port: 22
            state: enabled

        - name: Enable UFW
          ufw:
            state: enabled
    ```

4. **Configuración de Usuarios y Grupos**: Para agregar usuarios y configurar sus permisos:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Create user
          user:
            name: username
            state: present
            groups: sudo
            append: yes
    ```

5. **Configuración de SSH**: Para gestionar la configuración de SSH:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Copy SSH authorized keys
          authorized_key:
            user: username
            key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
            state: present
    ```

Estos son solo algunos ejemplos de scripts y playbooks que pueden ser útiles para administrar servidores Ubuntu con Ansible. Puedes personalizarlos según tus necesidades específicas y agregar más tareas según sea necesario.
