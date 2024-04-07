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


-----------------------
ejemplos de tareas de Ansible enfocadas en seguridad para servidores Ubuntu:

1. **Configuración de SSH para Autenticación de Claves y Deshabilitación de Acceso de Root**:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Disallow root login via SSH
          lineinfile:
            path: /etc/ssh/sshd_config
            regexp: '^PermitRootLogin'
            line: 'PermitRootLogin no'
            state: present
            backup: yes

        - name: Disable password authentication
          lineinfile:
            path: /etc/ssh/sshd_config
            regexp: '^PasswordAuthentication'
            line: 'PasswordAuthentication no'
            state: present
            backup: yes

        - name: Restart SSH service
          service:
            name: sshd
            state: restarted
    ```

2. **Instalación y Configuración de Fail2Ban**:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Install Fail2Ban
          apt:
            name: fail2ban
            state: present

        - name: Copy Fail2Ban configuration
          template:
            src: fail2ban.j2
            dest: /etc/fail2ban/jail.local
            owner: root
            group: root
            mode: '0644'

        - name: Restart Fail2Ban
          service:
            name: fail2ban
            state: restarted
    ```

    Contenido del archivo `fail2ban.j2`:

    ```ini
    [DEFAULT]
    ignoreip = 127.0.0.1/8 ::1
    bantime = 3600
    findtime = 600
    maxretry = 3
    ```

3. **Configuración de Firewall Avanzada con iptables o nftables**:

    Puedes utilizar módulos de Ansible como `iptables`, `nftables`, o `ufw` para configurar un firewall más avanzado según tus necesidades de seguridad específicas. Por ejemplo:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Configure iptables
          iptables:
            chain: INPUT
            state: present
            policy: DROP
          when: ansible_os_family == 'Debian'
    ```

    Este ejemplo configura una política predeterminada de denegar todo el tráfico de entrada en un servidor Ubuntu utilizando iptables.

4. **Configuración de Auditoría de Sistema con Auditd**:

    ```yaml
    ---
    - hosts: all
      become: true
      tasks:
        - name: Install auditd
          apt:
            name: auditd
            state: present

        - name: Ensure auditd service is running
          service:
            name: auditd
            state: started
            enabled: yes

        - name: Copy auditd rules
          copy:
            src: audit.rules
            dest: /etc/audit/rules.d/audit.rules
            owner: root
            group: root
            mode: '0640'

        - name: Reload auditd rules
          command: auditctl -R /etc/audit/rules.d/audit.rules
    ```

    En este ejemplo, se instala el paquete `auditd` y se configuran reglas de auditoría específicas.

