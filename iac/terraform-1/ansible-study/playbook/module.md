# Module

### module example

* yum

```yaml
- name: Install package
  yum:
    name: package_name
    state: present
  become: true
```

* apt

```yaml
- name: Install package
  apt:
    name: package_name
    state: present
  become: true
```

* copy
* file
* lineinfile
