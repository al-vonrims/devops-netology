# devops-netology

## Описание исключений в файле .gitingore для директории terraform:

1. `**/.terraform/*` Игнорирование всех директорий .terraform/ и её вложений
2. `*.tfstate` Игнорировать все файлы с расширением .tfstate
3. `*.tfstate.*` Игнорировать все файлы, содержащие в имени .tfstate.
4. `crash.log` Исключить все файлы с именем crash.log
5. `*.tfvars` Игнорировать все файлы с расширением .tfvars
6. `override.tf` Исключить все файлы с именем override.tf
7. `override.tf.json` Исключить все файлы с именем override.tf.json
8. `*_override.tf` Исключить все файлы оканчивающиеся на _override.tf
9. `*_override.tf.json` Исключить все файлы оканчивающиеся на *_override.tf.json
10. `!example_override.tf` Отслеживать файлы с именем example_override.tf даже, если они попадают под исключения выше
11. `*tfplan*` Игнорировать все файлы, содержащие в имени tfplan
12. `.terraformrc` Исключить все файлы с именем .terraformrc
13. `terraform.rc` Исключить все файлы с именем terraform.rc
14. New Line
 