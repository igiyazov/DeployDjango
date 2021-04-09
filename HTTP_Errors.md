# Errors with http
  
### Cors errors when you send get request to S3
You need just add ```cache: no-cache``` header in your request.
```javascript
        var myImage = document.querySelector('img');

        var myRequest = new Request('https://storage.yandexcloud.net/youcleve/media/inostrannye-yazyki/ispanskij/new-45/11.jpeg');

        fetch(myRequest, {
                method: 'GET',
                mode: 'cors',
                cache: 'no-cache',
                headers: {
                    Origin: window.location.origin,
                },
            })
            .then(response => response.blob())
            .then(function(myBlob) {
                var objectURL = URL.createObjectURL(myBlob);
                myImage.src = objectURL;
            });
```
[Answer1](https://stackoverflow.com/questions/55158189/cross-origin-requests-ajax-requests-to-aws-s3-sometimes-result-in-cors-error), [answer2](https://stackoverflow.com/questions/12498704/cached-non-cors-response-conflicts-with-new-cors-request) from stackoverflow
