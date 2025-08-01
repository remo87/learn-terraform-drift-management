# Learn Terraform Drift Management

This is a companion repository for the [Learn Terraform Drift Management tutorial](https://developer.hashicorp.com/terraform/tutorials/state/resource-drift). Follow along to learn more about state management.


pasos seguidos

- generar llave a usar para conectarse con ssh

``` bash
ssh-keygen -t rsa -C "remo87@gmail.com" -f ./key
```

- aplicar cambios

- introducir drift 
    - crear el security group y obtener el id `export SG_ID_TEMP=$(aws ec2 create-security-group --group-name "sg_web" --description "allow 8080" --output text)`
    - extraer el security group id `export SG_ID=$(echo $SG_ID_TEMP | awk '{print $1}')`
    - agregar crear un security group para acceder a la instancia por el puerto 8080 `aws ec2 authorize-security-group-ingress --group-name "sg_web" --protocol tcp --port 8080 --cidr 0.0.0.0/0`
    - asociar el security group a la instancia `aws ec2 modify-instance-attribute --instance-id $(terraform output -raw instance_id) --groups $SG_ID`

- resolver drift como manual
    - verificar si hay drift `terraform plan -refresh-only`
    - hacer que el plan refleje la infraestructure real `terraform apply -refresh-only`
    - agregar recursos a main
    - agregar security group a instancia
    - importar security group `terraform import aws_security_group.sg_web $SG_ID`
    - importar regla `terraform import aws_security_group_rule.sg_web "$SG_ID"_ingress_tcp_8080_8080_0.0.0.0/0`
    - applicar cambios `terraform apply`

- resolver drift usando import directive
    - verificar si hay drift `terraform plan -refresh-only`
    - hacer que el plan refleje la infraestructure real `terraform apply -refresh-only`
    - agregar recursos a main
    - agregar security group a instancia
    - agregar import para el security group y la regla
    - applicar cambios `terraform apply`

echo $SG_ID | awk '{print $1}'