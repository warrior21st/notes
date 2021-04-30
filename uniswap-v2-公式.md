## uniswap-v2-core formulas
### swap
    //x:交易对某个token的储量
    //y:交易对另一个token的储量
    //x_in:x输入数量
    //y_out:y输出数量
    x * y =k
    (x + x_in) * (y - y_out)=k
    (x+x_in)*y - (x+x_in)*y_out=K
    (x+x_in)*y_out=(x+x_in)*y - k
    y_out=((x+x_in)*y-k)/(x+x_in)
        =((x+x_in)*y-x*y)/(x+x_in)
        =((x+x_in)*y-x*y)/(x+x_in)
        = (x*y + x_in*y - x*y)/(x+x_in)
        = x_in*y/(x+x_in)
    //得出：输出金额=输入金额 * 输出储量 / (输入储量+输入金额) 

    (x + x_in) * (y - y_out)=k
    x*(y - y_out) +  x_in*(y - y_out)=k
    x_in*(y-y_out)=k-x*(y - y_out)
    x_in=(k-x*(y - y_out))/(y-y_out)
        =x*y-(x*(y - y_out))/(y-y_out)
        =(x*y-(x*y-x*y_out))/(y-y_out)
        =(x*y-x*y+x*y_out)/(y-y_out)
        =x*y_out/(y-y_out)
    //得出：输入金额=输入储量 * 输出金额 / (输出储量-输出金额) 

    //with fee
    //feerate= 0.3% = 0.003
    //已知x_in算y_out    
    x_in=x_in*(1-0.003)
        =x_in*0.997
        =x_in*997/1000

    y_out=x_in*997/1000 *y / (x+x_in*997/1000)
         =x_in*997*y/ 1000 / (x+x_in*997/1000)
         =x_in*997*y/((x+x_in*997/1000)*1000)
         =x_in*997*y/(x*1000 + x_in*997/1000*1000)
         =x_in*997*y/(x*1000 + x_in*997)
    //得出：输出金额=输入金额 * 997 * 输出储量 / (1000*输入储量 + 输入金额*997) 

    //已知y_out算x_in
    x_in=x * y_out * 1000 /(y-y_out)*997;
    //输入金额=输入储量 * 输出金额 * 1000 /(输出储量-输出金额) * 997

    //在withfee计算中的注意点：
    //设x=100 y=200 则k=x*y=20000
    //交易输入10个x
    //x_in=10
    //x=100 y=200 k=20000
    //y_out=10*997*200/(100*1000 + 10*997)=18.132217
    //x=100+10=110
    //y=200-18.132217=181.867783
    //k=110*181.867783=20005.456033463 
    //交易后的k > 交易前的k

### mint(addLiquidity)
    //MINIMUM_LIQUIDITY：permanently lock the first MINIMUM_LIQUIDITY tokens
    //x_add：添加的token0数量
    //y_add：添加的token1数量
    //totalSupply：原lptoken总量
    //liquidity：获得的lptoken数量
    //x_reserve：原token0储量
    //y_reserve：原token1储量
    MINIMUM_LIQUIDITY=10**3
    if(totalSupply==0) //如果是首次添加流动性,lptoken数量为 x_add * y_add的平方根 - 锁定的最小流动性数量
    {
        liquidity= Math.sqrt(x_add * y_add))-MINIMUM_LIQUIDITY
    }
    else //否则计算x_add和y_add对原有储量的增幅 * 原lptoken总数量，两者取其小
    {
        //x_increase_rate:x增幅
        //y_increase_rate:y增幅
        x_increase_rate=x_add / x_reserve
        y_increase_rate=y_add / y_reserve

        liquidity_x =x_increase_rate * totalSupply
                    =x_add / x_reserve * totalSupply
                    =x_add * totalSupply / x_reserve

        liquidity_y =y_increase_rate * totalSupply        
                    =y_add / y_reserve * totalSupply
                    =y_add * totalSupply / y_reserve

        liquidity = Math.min(
            liquidity_x,
            liquidity_y
        )
        liquidity=Math.min(
            x_add * totalSupply / x_reserve,
            y_add * totalSupply / y_reserve
        );

        //得出：lptoken数量= （x增量 * 总lptoken总量 / 原x总储量） 与 （y增量 * 总lptoken总量 / 原y总储量） 的最小值
    }

### burn(removeLiquidity)
    //liquidity：要销毁的lptoken数量
    //x_decrease：获取的x数量
    //y_decrease：获取的y数量
    //x_balance：合约的token0余额
    //y_balance：合约的token1余额
    //liquidity_percent：要销毁的lptoken数量占比
    //totalSupply：lptoken总量

    liquidity_percent= liquidity / totalSupply

    x_decrease =liquidity_percent * x_balance
               =liquidity / totalSupply * x_balance
               =liquidity * x_balance / totalSupply 

    y_decrease =liquidity_percent * y_balance
               =liquidity / totalSupply * y_balance
               =liquidity * y_balance / totalSupply

    //得出：x输出数量= 要销毁的lptoken数量 * 合约x余额 / 总lptoken数量，y输出数量=要销毁的lptoken数量 * 合约y余额 / 总lptoken数量
    
### 价格维护公式
    //x：交易对其中一个token的当前储量
    //y：交易对另一个token的当前储量
    //price_y：目标价格（以y为计价单位）
    //x_target：价格抵达price_y时x储量
    //y_target：价格抵达price_y时y储量
    //y_increment：价格抵达price_y时需要增加的y数量
    //x_increment：价格抵达price_y时需要增加的x数量
    target_x*target_y=k
    target_x*target_y=x*y
    target_x=x*y/target_y    

    target_y/target_x=price_y
    target_y=price_y*target_x
    target_y=price_y*x*y/target_y
    target_y*target_y=price_y*x*y
    target_y=sqrt(price_y*x*y)
    y_increment=target_y-y
               =sqrt(price_y*x*y)-y

    target_x=target_y/price_y
    target_x=sqrt(price_y*x*y)/price_y
    x_increment=sqrt(price_y*x*y)/price_y-x

    //withfee
    //fee_rate=0.3%=0.003=3/1000
    //swap输出金额计算公式：y_out=x_in*997*y/(x*1000 + x_in*997)
    price_y=(y-y_out)/(x+x_in)
    price_y=(y-x_in*997*y/(x*1000 + x_in*997))/(x+x_in)
    price_y*(x + x_in)=y-x_in*997*y/(x*1000 + x_in*997)
    y-price_y*x-price_y*x_in=x_in*997*y/(x*1000+x_in*997)
    x_in*997*y=(1000*x + 997*x_in)*(y-price_y*x-price_y*x_in)
    997*x_in*y=1000*x*y - 1000*x*price_y*x^2 - 1000*x*price_y*x_in + 997*x_in*y - 997*x_in*price_y*x - 997*x_in²*price_y
    997*x_in*y=1000*x*y - 1000*price_y*x^2 - 1000*x*price_y*x_in + 997*x_in*y - 997*x_in*price_y*x - 997*x_in²*price_y
    0=1000*x*y - 1000*price_y*x² - 1000*x*price_y*x_in - 997*x*x_in*price_y - 997*price_y*x_in²
    0=1000*x*y - 1000*price_y*x² - (1000*x*price_y*x_in + 997*x*x_in*price_y) - 997*price_y*x_in²
    0=1000*x*y - 1000*price_y*x² - 1997*x*price_y*x_in - 997*price_y*x_in²

    //将以上等式中1000*x*y-1000*price_y*x²视为c，-1997*x*price_y视为b，-997*price_y视为a，以上等式可以视为二元一次方程的一般形式：ax²+bx+c=0（a≠0）
    //二元一次方程求根公式：root=-[-b±√(b²-4ac)]/2a
    //Δ>0时，方程有两个不相等的实数根
    //Δ=0时，方程有两个相等的实数根
    //Δ<时，方程无实数根，但有2个共轭复根    
    Δ=(1997*x*price_y*-1)^2-4*997*price_y*-1*(1000*x*y-1000*price_y*x²)
    x_in=(-(-1997*x*price_y)±sqrt((-1997*price_y*x)-))/2*(-997*price_y)
    x_in=-1*((1997*x*price_y*-1)±sqrt((1997*x*price_y*-1)^2-4*997*price_y*-1*(1000*x*y-1000*price_y*x²)))/(2*997*price_y*-1)
