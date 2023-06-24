
```{json}
{  
    "result": {  
        "code": "S0000",  
        "message": "Success"  
    },  
    "paymentMethod": {  
        "paymentBrandList": [  
            {  
                "brandName": "Visa",  
                "brandImage": "https://VISA_LOGO_IMAGE_URL.com",  
                "type": "card",  
                "paymentInfo": [  
                    {  
                        "key": "Card Number",  
                        "optional": false,  
                        "type": "number",  
                        "minLength": "12",  
                        "maxLength": "19"  
                    },  
                    {  
                        "key": "Expiry date",  
                        "optional": false,  
                        "type": "number",  
                        "minLength": "4",  
                        "maxLength": "4",  
                        "defaultValue": "MMYY",  
                        "regEx": "/^(0[1-9]|1[0-2])\\d{2}$/"  
                    },  
                    {  
                        "key": "CVC",  
                        "optional": true,  
                        "type": "number",  
                        "minLength": "3",  
                        "maxLength": "4"  
                    },  
                    {  
                        "key": "Cardholder Name",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    }  
                ],  
                "authenticationInfo": [  
                    {  
                        "key": "Card Number",  
                        "optional": false,  
                        "type": "number",  
                        "minLength": "12",  
                        "maxLength": "19"  
                    },  
                    {  
                        "key": "Cardholder Name",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Cardholder Email",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "254",  
                        "regEx": "/^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$/"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "optional": true,  
                        "type": "Object"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "Country",  
                        "optional": false,  
                        "type": "string"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "State Or Province",  
                        "optional": true,  
                        "type": "string"  
                    },                      
                    {  
                        "key": "Billing Address",  
                        "subKey": "City",  
                        "optional": false,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "addressLine1",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "addressLine2",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "addressLine3",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Billing Address",  
                        "subKey": "Postal Code",  
                        "optional": false,  
                        "type": "string"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "optional": true,  
                        "type": "Object"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "Country",  
                        "optional": false,  
                        "type": "string"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "State Or Province",  
                        "optional": true,  
                        "type": "string"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "City",  
                        "optional": false,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "addressLine1",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "addressLine2",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "addressLine2",  
                        "optional": true,  
                        "type": "string",  
                        "maxLength": "50"  
                    },  
                    {  
                        "key": "Delivery Address",  
                        "subKey": "Postal Code",  
                        "optional": false,  
                        "type": "string"  
                    }  
                ]  
            },  
            {  
                  "brandName": "Alipay",
                  "type": "e-wallet", 
                  "brandImage": "https://ALIPAY_LOGO_IMAGE_URL.com"  
            },  
            {  
                "brandName": "AlipayHK",
                  "type": "e-wallet", 
                "brandImage": "https://ALIPAYHK_LOGO_IMAGE_URL.com"  
            },
             {
                  "brandName": "Alipay+",
                  "type": "e-wallet", 
                  "brandImage": "https://ALIPAYPLUS_LOGO_IMAGE_URL.com",
                  "promotionInfo": [
                      "en_US":"RM1 Voucher",
                      "zh_CN":"1元 红包"
                  ]
            },
        ]  
    }  
}  
```