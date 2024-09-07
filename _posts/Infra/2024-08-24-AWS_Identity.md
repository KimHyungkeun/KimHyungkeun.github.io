---
title: AWS 사용자 인증
date: 2024-08-24 00:00:00 +00:00
categories: [Infra, AWS]
tags:
  [
    Infra,
    AWS
  ]
---

## 1. AWS Identity and Access Management (AWS IAM)

### 1. IAM = Identity and Access Management, Global service

⇒ 즉, AWS에서의 사용자 user, id 관리 서비스

- **Root** account created by default, shouldn’t be used or shared
    
    ⇒ Root 계정은 기본적으로 생성되는 계정이며, 타인에게 사용 또는 공유되지 않음
    
- **Users** are people within your organization, and can be grouped
    
    ⇒ User 사용자는 organization에 속하고, group에 넣는것이 가능
    

- **Groups** only contain users, not other groups
⇒ Group은 user만을 포함 가능하고, 다른 group을 포함하는것은 불가
- Users ****don’t have to belong to a group, and user can belong to multiple groups
    
    ⇒ User는 하나의 group에만 속하는 것이 아닌, 여러개의 group에 속할수도 있다
    

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/8ae3c86e-a165-4b40-9776-80eb5662e31a/Untitled.png)

### 2. IAM: Permissions (권한)

- Users or Groups can be assigned JSON documents called policies
    
    ⇒ **User와 Group은 policies라 불리는 JSON 문서에 assign 된다**
    
- These policies define the permissions of the users
    
    ⇒ 이러한 polices는 user의 특권을 정의한다
    
- In AWS you apply the least privilege principle: don’t give
more permissions than a user needs
    
    ⇒ AWS에서는 사용자에게 특권 정책을 최소한으로 지정한다. 즉, user가 가진 권한보다 더 많은 권한을 주지 않도록 한다
    

### 3. IAM Policies inheritance

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/821b8945-4381-4e00-a934-427caf7e6c97/Untitled.png)

### 4. IAM Policies Structure

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/81c3f858-af1e-426c-bbb2-45ba2b91014c/Untitled.png)

- 구조
    - **Version**: policy language version, always include “2012-10-17”
        
        ⇒ policy language 버전. 현재 2012-1017
        
    - **Id:** an identifier for the policy (optional)
        
        ⇒ policy의 식별ID
        
    - **Statement**: one or more individual statements (required)
    ⇒ 하나 이상의 개별 statement를 가짐 (필수사항)

- Statements consists of
    - **Sid**: an identifier for the statement (optional)
        
        ⇒ Statement 식별 ID
        
    - **Effect:** whether the statement allows or denies access (Allow, Deny)
        
        ⇒ Statement의 허용, 거부 여부
        
    - **Principal**: account/user/role to which this policy applied to
        
        ⇒ account/user/role 에 대한 pricipal
        
    - **Action**: list of actions this policy allows or denies
    - **Resource**: list of resources to which the actions applied to
    - **Condition**: conditions for when this policy is in effect (optional)

### 5. IAM – Password Policy

- Strong passwords = higher security for your account
    
    ⇒ password 강도가 높을수록, 로그인 보안에 더욱 힘이 보태짐
    
- In AWS, you can setup a password policy:
    - Set a minimum password length
        
        ⇒ password 최소 길이 설정 가능
        
    - Require specific character types
        - including uppercase letters ⇒ 대문자포함
        - lowercase letters ⇒ 소문자
        - numbers ⇒ 숫자
        - non-alphanumeric characters ⇒ 특수 문자
    - Allow all IAM users to change their own passwords 
    ⇒ 모든 IAM user가 본인 password 변경 가능
    - Require users to change their password after some time (password expiration)
        
        ⇒ 패스워드 기간 만료 시, 새 패스워드 설정 가능하도록 진행
        
    - Prevent password re-use
        
        ⇒ 패스워드 재사용 방지
        

### 6. Multi Factor Authentication - MFA (시험에 꼭 나옴)

- Users have access to your account and can possibly change configurations or delete resources in your AWS account
- You want to protect your Root Accounts and IAM users
- MFA = 사용자가 정한 password + 본인이 가진 security 모듈
- MFA의 장점 : password가 도난당하거나 해킹당해도, 추가 보안 설정이 있기에 안전

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/5059608e-728e-4486-ae59-12207ca4b452/Untitled.png)

- AWS에서 사용 가능한 MFA 디바이스
    - Virtual MFA device
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/e205e702-52a4-4955-93f2-46ce6107e3fa/Untitled.png)
    
    - Universal 2nd Factor (U2F) Security Key
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/e380293d-5269-4fe5-a3d9-7b90bb9a21ba/Untitled.png)
    
    - Hardware Key Fob MFA Device
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/325fe3d5-d3bf-4bb7-a590-524276e6e3d2/Untitled.png)
    
    - Hardware Key Fob MFA Device for AWS GovCloud (US)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/add9bb16-cce5-4cd0-b987-a6e553da4823/Untitled.png)
    

### 7. How can users access AWS

- AWS에 접근하는 방법
    - **AWS Management Console (웹 콘솔, password + MFA)**
    - **AWS Commnad Line (CLI, access key로 보호된다)**
    - **AWS Software Developer Kit (SDK, for code : access key로 보호된다)**
- Access Key는 AWS Console에서 생성 가능
- User는 access key를 관리 가능함
- Access key는 secret한 정보이다. 절대 공유 하지 말 것
- Access Key ID ~= username
- Secret Access Key ~= password

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/3dd412d3-d91c-49e1-b58a-81b1a9411c36/Untitled.png)

### 8. What’s the AWS SDK?

- AWS Software Development Kit (AWS SDK)
- Language-specific APIs (set of libraries)
    
    ⇒ 언어 특화적 API (라이브러리 모음)
    
- Enables you to access and manage AWS services programmatically
    
    ⇒ 프로그래밍 적으로 AWS 서비스에 접근 및 관리를 진행
    
- Embedded within your application
- Supports
    - SDKs (JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++)
    - Mobile SDKs (Android, iOS, …)
    - IoT Device SDKs (Embedded C, Arduino, …)
- Example: AWS CLI is built on AWS SDK for Python
    
    ⇒ 예시 : AWS CLI는 python용 AWS SDK에 build 된다
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/ca56279f-cf1a-4a76-b580-49ed53b50748/Untitled.png)
    
    - CloudShell 예제
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/a8877b99-5881-4e26-adcb-7d41c084c52d/Untitled.png)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/6e89b1a2-5aad-463f-849d-50bf1e129368/Untitled.png)
    
    ### 9. IAM Roles for Services
    
    - Some AWS service will need to perform actions on your behalf
        
        ⇒ 몇몇 AWS 서비스는 사용자를 대신하여 action이 취해진다
        
    - To do so, we will assign **permissions** to AWS services with **IAM Roles**
        
        ⇒ AWS 서비스에 IAM Roles라는 것을 이용하여 permission을 부여한다
        
    - Common roles:
        - EC2 Instance Roles (EC2 인스턴스)
        - Lambda Function Roles (Lambda Function)
        - Roles for CloudFormation (클라우드 포메이션 role)
        
    
    ### 10. IAM Security Tools
    
    - **IAM Credentials Report (account-level, 계정들 리스트를 보여줌)**
    • a report that lists all your account's users and the status of their various credentials
    ⇒ 모든 account user 리스트와 다양한 crendentail 상태에 대한 리스트에 대한 리포트이다
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/cb6e7041-3ba5-4b66-abb4-1226a28b8c5b/Untitled.png)
    
    - **IAM Access Advisor (user-level, 한 사용자가 최근 접속했던 service들 리스트)**
        - Access advisor shows the service permissions granted to a user and when those services were last accessed.
            
            ⇒ user에게 부여했던 service 권한 및 마지막으로 접근한 service에 대한 리스트를 보여준다
            
        - You can use this information to revise your policies.
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/97f8f071-477d-4db3-a9c0-4dad109b848c/ea6e602c-6b75-442c-8edb-799d4c8c60dc/Untitled.png)
        
    
    ### 11. IAM Guidelines & Best Practices
    
    - Don’t use the root account except for AWS account setup
    ⇒ AWS 계정 생성 외에는 root 계정은 사용하지 말것
    - One physical user = One AWS user
        
        ⇒ 하나의 유저는 곧 하나의 AWS 유저
        
    - Assign users to groups and assign permissions to groups
        
        ⇒ group에 user를 부여하는 것이고, group에 permission을 부여하는 것이다
        
    - Create a strong password policy
        
        ⇒ 더욱 꼼꼼한 패스워드 정책을 수립할 것 
        
    - Use and enforce the use of Multi Factor Authentication (MFA)
        
        ⇒ MFA 사용을 적극적으로 할 것
        
    - Create and use Roles for giving permissions to AWS services
        
        ⇒ Role을 생성하고, AWS service에 permission들을 부여할 것
        
    - Use Access Keys for Programmatic Access (CLI / SDK)
        
        ⇒ 프로그래밍 적인 방법으로 Access Key를 사용할 것
        
    - Audit permissions of your account using IAM Credentials Report & IAM Access Advisor
        
        ⇒ IAM Credentials Report와 IAM Access Advisor를 사용한 permission 검사를 진행할 것 
        
    - Never share IAM users & Access Keys
        
        ⇒ IAM user와 Access key는 타인과 공유하지 말 것
        
    
    ### 정리
    
    - Users: AWS console에 대한 패스워드를 가지는 실질적인 유저
    - Groups: Users를 포함
    - Policies: Users와 Groups에 대한 상세내용을 갖고있는 JSON Documents
    - Roles: EC2 인스턴스나 AWS Service를 위해 설정하는 Policies 모음
    - Security: MFA + Password Policy
    - AWS CLI: CLI를 이용한 AWS 서비스 관리
    - AWS SDK: 프로그래밍 언어를 이용한 AWS 서비스 관리
    - Access Keys: CLI 또는 SDK를 이용한 AWS 접근
    - Audit: IAM Credential Reports & IAM Access Advisor
    
    