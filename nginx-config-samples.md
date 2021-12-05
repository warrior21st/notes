# nginx config samples

## rewrite sample 

    # file directory: ./xxximgs/
    location /xxximgs {
        alias xxximgs;            
    }
    
    location /yyyimages {
        rewrite /yyyimages/(.+)/ /xxximgs/$1.png permanent;
    }


    # file directory: ./aaaimgs/
    location /aaaimgs {
        alias aaaimgs;            
    }

    location /bbbimages {
        rewrite (.+) /aaaimgs/aaaimage.png permanent;
    }