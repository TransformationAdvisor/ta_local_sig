## Verify Transformation Advisor Images

### Pre-req

Install `openssl` tool.

### Steps to verify

1. Download `transformation-advisor-local-<version>.tgz` from GA folder and uncompressed the file.
      
        e.g. transformation-advisor-local-3.3.0.tgz
        tar -xzf transformation-advisor-local-3.3.0.tgz
        cd signing_result
        ls
        # sample contents in the folder
        ibm@ks-MacBook-Pro signing_result % ls
        certificate.pem        public.pem
        chain0.pem             transformation-advisor-local-3.3.0.zip.sig

2. Download TA Local from https://www.ibm.com/garage/method/practices/learn/ibm-transformation-advisor/:

        Assume the TA local file is called: transformation-advisor-local-3.3.0.zip

3. Copy this file to the `signing_result` folder

        cp transformation-advisor-local-3.3.0.zip signing_result

4. verify the signature:

        # run
        
        openssl dgst -sha256 -verify public.pem \
        -signature transformation-advisor-local-3.3.0.zip.sig \
        transformation-advisor-local-3.3.0.zip

        # you shall see

        Verified OK

6. Inspect the public cert

        # run
        openssl x509 -text -in certificate.pem

        # verify

        Certificate:
        Data:
        Version: 3 (0x2)
        Serial Number:
        02:cb:ad:38:55:75:af:2e:c2:68:73:14:63:3a:44:75
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = US, O = "DigiCert, Inc.", CN = DigiCert Trusted G4 Code Signing RSA4096 SHA384 2021 CA1
        Validity
        Not Before: Mar 14 00:00:00 2022 GMT
        Not After : Mar 13 23:59:59 2024 GMT
        Subject: C = US, ST = New York, L = Armonk, O = International Business Machines Corporation, OU = IBM CCSS, CN = International Business Machines Corporation

7. Inspect the public key

        # run

        openssl rsa -noout -text -inform PEM -in public.pem -pubin

        # compare the Modulus section output with the output of the previous command.
        # to find out this public key is indeed in the public cert
        
        e.g.

        RSA Public-Key: (4096 bit)
        Modulus:
        00:c3:ed:2d:4e:3f:6f:31:e3:e9:da:a4:77:01:16:
        8c:7e:ae:84:53:24:45:6b:4e:fb:b0:bd:e0:3b:48:
        b5:c6:25:f9:65:7d:2a:1c:55:5f:43:83:66:43:1f:
        ...

        and

        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (4096 bit)
                Modulus:
                    00:c3:ed:2d:4e:3f:6f:31:e3:e9:da:a4:77:01:16:
                    8c:7e:ae:84:53:24:45:6b:4e:fb:b0:bd:e0:3b:48:
                    b5:c6:25:f9:65:7d:2a:1c:55:5f:43:83:66:43:1f:
                    ...

8. Verify the IBM public certificate validity using `digicert.com`

        openssl ocsp -no_nonce -issuer chain0.pem \
        -cert certificate.pem -VAfile chain0.pem \
        -text -url http://ocsp.digicert.com -respout ocsptest
        
        ...
        Response verify OK
        certificate.pem: good
        This Update: Aug 12 08:33:01 2022 GMT
        Next Update: Aug 19 07:48:01 2022 GMT
