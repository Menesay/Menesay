
# crypto failures
# 10.66.168.30

# nmap

```
PORT   STATE SERVICE REASON         VERSION
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.59 ((Debian))
|_http-server-header: Apache/2.4.59 (Debian)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to /

```


# web - port 80

ìndex.html
```
<p>You are logged in as guest:**********************************************************************
<p>SSO cookie is protected with traditional military grade en<b>crypt</b>ion
<!-- TODO remember to remove .bak files-->
```

secure cookie : FLCiPws6%2FfHrUFLdniTOodVfH2FL1f4PJELh8B2FLveWLNe5qmzsFLsBg1FLlu2FsFLUJ0OHi2DsusFLjnfpHT9fGvsFLoe8TMIZmEicFL5pUTu%2FW%2FGGYFLDip13zV7JBQFLPY1bGTIEmKwFLtB5FygXSWiIFLOr%2FNoHO5pwIFLBj6OiKDlDFYFLJZC0mOM%2FGQoFL7snd1GJpBFQFLVHMq0YsKbRoFLg4wSqrmKQagFLtDXmNFXx0w2FLU9f8dlcLSzkFLU6Xh1xoiDRMFLyggCpe1CMdYFLddeTLdqxQIYFLhkHHQgyUK%2FMFLX5Fed2ie9c2FL3FClearIJ%2FcFLuA5iAwFpw4YFLndj%2FNa41mbkFLdtPenFw7wpE

```
ffuf -u http://10.66.168.30/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-small-files.txt -e .php~,.php.bak,.php.old,.phps,.bak,.zip -fc 404

index.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 833ms]
index.php.bak           [Status: 200, Size: 1979, Words: 282, Lines: 96, Duration: 1421ms]
config.php              [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 148ms]
.htaccess.php.bak       [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 140ms]
.htaccess.bak           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 154ms]
.htaccess.zip           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 154ms]
.htaccess.php~          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 156ms]
.htaccess.php.old       [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 155ms]
.htaccess.phps          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 155ms]
.htaccess               [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 156ms]
[WARN] Caught keyboard interrupt (Ctrl-C)
```

index.php.bak  
```php
<?php
include('config.php');

function generate_cookie($user,$ENC_SECRET_KEY) {
    $SALT=generatesalt(2);
    
    $secure_cookie_string = $user.":".$_SERVER['HTTP_USER_AGENT'].":".$ENC_SECRET_KEY;

    $secure_cookie = make_secure_cookie($secure_cookie_string,$SALT);

    setcookie("secure_cookie",$secure_cookie,time()+3600,'/','',false); 
    setcookie("user","$user",time()+3600,'/','',false);
}

function cryptstring($what,$SALT){

return crypt($what,$SALT);

}


function make_secure_cookie($text,$SALT) {

$secure_cookie='';

foreach ( str_split($text,8) as $el ) {
    $secure_cookie .= cryptstring($el,$SALT);
}

return($secure_cookie);
}


function generatesalt($n) {
$randomString='';
$characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
for ($i = 0; $i < $n; $i++) {
    $index = rand(0, strlen($characters) - 1);
    $randomString .= $characters[$index];
}
return $randomString;
}



function verify_cookie($ENC_SECRET_KEY){


    $crypted_cookie=$_COOKIE['secure_cookie'];
    $user=$_COOKIE['user'];

    // user cookie : user agnt : key
    $string=$user.":".$_SERVER['HTTP_USER_AGENT'].":".$ENC_SECRET_KEY;

    // ilk iki karakter salt
    // User-Agent : Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
    // Mo
    $salt=substr($_COOKIE['secure_cookie'],0,2);

    if(make_secure_cookie($string,$salt)===$crypted_cookie) {
        return true;
    } else {
        return false;
    }
}


if ( isset($_COOKIE['secure_cookie']) && isset($_COOKIE['user']))  {

    $user=$_COOKIE['user'];

    if (verify_cookie($ENC_SECRET_KEY)) {
        
    if ($user === "admin") {
   
        echo 'congrats: ******flag here******. Now I want the key.';

            } else {
        
        $length=strlen($_SERVER['HTTP_USER_AGENT']);
        print "<p>You are logged in as " . $user . ":" . str_repeat("*", $length) . "\n";
	    print "<p>SSO cookie is protected with traditional military grade en<b>crypt</b>ion\n";    
    }

} else { 

    print "<p>You are not logged in\n";
   

}

}
  else {

    generate_cookie('guest',$ENC_SECRET_KEY);
    
    header('Location: /');


}
?>

```

forger.php
```php
<?php
$cookie = 'FLCiPws6%2FfHrUFLdniTOodVfH2FL1f4PJELh8B2FLveWLNe5qmzsFLsBg1FLlu2FsFLUJ0OHi2DsusFLjnfpHT9fGvsFLoe8TMIZmEicFL5pUTu%2FW%2FGGYFLDip13zV7JBQFLPY1bGTIEmKwFLtB5FygXSWiIFLOr%2FNoHO5pwIFLBj6OiKDlDFYFLJZC0mOM%2FGQoFL7snd1GJpBFQFLVHMq0YsKbRoFLg4wSqrmKQagFLtDXmNFXx0w2FLU9f8dlcLSzkFLU6Xh1xoiDRMFLyggCpe1CMdYFLddeTLdqxQIYFLhkHHQgyUK%2FMFLX5Fed2ie9c2FL3FClearIJ%2FcFLuA5iAwFpw4YFLndj%2FNa41mbkFLdtPenFw7wpE';

// Cookie oluşturulurken kullanılan TAM User-Agent
$ua = 'Mozilla/5.0';
// URL decode
$cookie = rawurldecode($cookie);
// İlk iki karakter = salt
$salt = substr($cookie, 0, 2);
echo "[+] Salt: $salt\n";
// PHP crypt() çıktısı klasik DES için 13 karakterdir.
// İlk blok guest: + UA'nın ilk 2 karakteridir.
$old_plaintext = "guest:" . substr($ua, 0, 2);
$new_plaintext = "admin:" . substr($ua, 0, 2);
echo "[+] Old block plaintext: $old_plaintext\n";
echo "[+] New block plaintext: $new_plaintext\n";
// Yeni ilk 13 karakterlik crypt bloğunu üret
$new_block = crypt($new_plaintext, $salt);
echo "[+] New crypt block: $new_block\n";
// Eski ilk blok 13 karakter:
// 2 karakter salt + 11 karakter DES crypt output
$old_block = substr($cookie, 0, 13);
echo "[+] Old crypt block: $old_block\n";
// İlk bloğu değiştir, geri kalanını aynen bırak
$forged_cookie = $new_block . substr($cookie, 13);
echo "\n========== FORGED COOKIES ==========\n\n";
echo "user=admin\n\n";
echo "secure_cookie=" . rawurlencode($forged_cookie) . "\n";
?>
```



```
congrats: THM{ok_you_f0und_w3b_fl4g_6cbe2bc}. Now I want the key.
```

# flag 1 = THM{ok_you_f0und_w3b_fl4g_6cbe2bc}

# flag 2 = `THM{Traditional_Own_Crypto_is_Always_Surprising!_and_this_hopefully_is_not_easy_to_crack_e41d20b5b0989cac65ed4a090cace944bf30e6d3ab88f9d447f52fd2140525b9}`