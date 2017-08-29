
delimiter //
create trigger Tb_US before update on us for each row
begin
set @t = 0;
if (new.trade_sr_df_m  < 0) then 
call check_value(new.currency, 'USD', @a);
if (@a > 0 ) then
if (new.curr_sply_m > abs(new.trade_sr_df_m) ) then
if (new.currency ='POUND') then  
call up_supply('uk',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='EURO') then 
call up_supply('eu',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='rupee') then 
call up_supply('india',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='YEN') then 
call up_supply('jp',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

end if;

else 

if (new.currency ='POUND') then  

call sl_pair ('POUND', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('us', new.currency, 'USD-POUND','USD','uk',@diff, @r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('uk',@pay, 'USD');
call up_e_rate(@curr_pair,'POUND', 'USD', @b);
set new.ex_rate = @b;
update uk set ex_rate = 1/@b where currency=  'USD';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;

elseif (new.currency = 'euro') then
call sl_pair ('euro', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('us', new.currency, @curr_pair,'USD','eu',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('eu',@pay, 'USD');
call up_e_rate(@curr_pair,'euro', 'USD', @b);
set new.ex_rate = @b;
update eu set ex_rate = 1/@b where currency=  'USD';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;

elseif ( new.currency = 'yen') then 
call sl_pair ('yen', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('us', new.currency, @curr_pair,'USD','jp',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('jp',@pay, 'USD');
call up_e_rate(@curr_pair,'yen', 'USD', @b);
set new.ex_rate = @b;
update jp set ex_rate = 1/@b where currency=  'USD';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;

elseif (new.currency = 'rupee') then
call sl_pair ('rupee', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('us', new.currency, @curr_pair,'USD','india',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('india',@pay, 'USD');
call up_e_rate(@curr_pair,'rupee', 'USD', @b);
set new.ex_rate = @b;
update india set ex_rate = 1/@b where currency=  'USD';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;

end if;
end if;
end if;
end if ;

if (@t= 1 && new.currency = 'USD') then 
set new.curr_sply_m = new.curr_sply_m - @pay;
end if ;

end //

/* trigger for UK table*/
create trigger Tb_UK before update on uk for each row
begin
set @t = 0;
if (new.trade_sr_df_m  < 0) then 
call check_value(new.currency, 'POUND', @a);
if (@a > 0 ) then
if (new.curr_sply_m > abs(new.trade_sr_df_m) ) then
if (new.currency ='USD') then  
call up_supply('us',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='EURO') then 
call up_supply('eu',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='rupee') then 
call up_supply('india',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='YEN') then 
call up_supply('jp',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

end if;

else 

if (new.currency ='USD') then  

call sl_pair ('USD', 'POUND', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('uk', new.currency, 'USD-POUND','POUND','us',@diff, @r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('us',@pay, 'POUND');
call up_e_rate(@curr_pair,'USD', 'POUND', @b);
set new.ex_rate = @b;
update us set ex_rate = 1/@b where currency=  'POUND';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;



elseif (new.currency = 'euro') then
call sl_pair ('euro', 'POUND', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('uk', new.currency, @curr_pair,'POUND','eu',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('eu',@pay, 'POUND');
call up_e_rate(@curr_pair,'euro', 'POUND', @b);
set new.ex_rate = @b;
update eu set ex_rate = 1/@b where currency=  'POUND';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;


elseif ( new.currency = 'yen') then 
call sl_pair ('yen', 'POUND', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('uk', new.currency, @curr_pair,'POUND','jp',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('jp',@pay, 'POUND');
call up_e_rate(@curr_pair,'YEN', 'POUND', @b);
set new.ex_rate = @b;
update jp set ex_rate = 1/@b where currency=  'POUND';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;


elseif (new.currency = 'rupee') then
call sl_pair ('rupee', 'POUND', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('uk', new.currency, @curr_pair,'POUND','india',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('india',@pay, 'POUND');
call up_e_rate(@curr_pair,'rupee', 'POUND', @b);
set new.ex_rate = @b;
update india set ex_rate = 1/@b where currency=  'POUND';
update currency_exch set latest_sp_m= @pay where currency_pair = @curr_pair;



end if;
end if;
end if;
end if ;

if (@t= 1 && new.currency = 'POUND') then 
set new.curr_sply_m = new.curr_sply_m - @pay;
end if ;

end //

/* trigger for eu table */
create trigger Tb_EU before update on eu for each row
begin
set @t = 0;
if (new.trade_sr_df_m  < 0) then 
call check_value(new.currency, 'euro', @a);
if (@a > 0 ) then
if (new.curr_sply_m > abs(new.trade_sr_df_m) ) then
if (new.currency ='POUND') then  
call up_supply('uk',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='USD') then 
call up_supply('us',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='rupee') then 
call up_supply('india',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='YEN') then 
call up_supply('jp',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

end if;

else 

if (new.currency ='POUND') then  

call sl_pair ('POUND', 'euro', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('eu', new.currency, @curr_pair,'euro','uk',@diff, @r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('uk',@pay, 'euro');
call up_e_rate(@curr_pair,'POUND', 'EURO', @b);
set new.ex_rate = @b;
update uk set ex_rate = 1/@b where currency=  'EURO';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;


elseif (new.currency = 'USD') then

call sl_pair ('euro', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('eu', new.currency, @curr_pair,'euro','us',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('us',@pay, 'euro');
call up_e_rate(@curr_pair,'USD', 'EURO', @b);
set new.ex_rate = @b;
update us set ex_rate = 1/@b where currency=  'EURO';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;

elseif ( new.currency = 'yen') then 
call sl_pair ('yen', 'euro', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('eu', new.currency, @curr_pair,'euro','jp',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('jp',@pay, 'euro');
call up_e_rate(@curr_pair,'YEN', 'EURO', @b);
set new.ex_rate = @b;
update jp set ex_rate = 1/@b where currency=  'EURO';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;

elseif (new.currency = 'rupee') then
call sl_pair ('rupee', 'euro', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('eu', new.currency, @curr_pair,'euro','india',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('india',@pay, 'euro');
call up_e_rate(@curr_pair,'rupee', 'EURO', @b);
set new.ex_rate = @b;
update india set ex_rate = 1/@b where currency=  'EURO';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;


end if;
end if;
end if;
end if ;

if (@t= 1 && new.currency = 'euro') then 
set new.curr_sply_m = new.curr_sply_m - @pay;
end if ;

end //

/* trigger of JP */
create trigger Tb_JP before update on jp for each row
begin
set @t = 0;
if (new.trade_sr_df_m  < 0) then 
call check_value(new.currency, 'yen', @a);
if (@a > 0 ) then
if (new.curr_sply_m > abs(new.trade_sr_df_m) ) then
if (new.currency ='POUND') then  
call up_supply('uk',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='USD') then 
call up_supply('us',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='rupee') then 
call up_supply('india',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='euro') then 
call up_supply('eu',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

end if;

else 

if (new.currency ='POUND') then  

call sl_pair ('POUND', 'yen', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('jp', new.currency, @curr_pair,'yen','uk',@diff, @r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('uk',@pay, 'yen');
call up_e_rate(@curr_pair,'POUND', 'YEN', @b);
set new.ex_rate = @b;
update uk set ex_rate = 1/@b where currency=  'YEN';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;


elseif (new.currency = 'USD') then

call sl_pair ('yen', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('jp', new.currency, @curr_pair,'yen','us',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('us',@pay, 'yen');
call up_e_rate(@curr_pair,'USD', 'YEN', @b);
set new.ex_rate = @b;
update us set ex_rate = 1/@b where currency=  'YEN';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;


elseif ( new.currency = 'euro') then 
call sl_pair ('yen', 'euro', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('jp', new.currency, @curr_pair,'yen','eu',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('eu',@pay, 'yen');
call up_e_rate(@curr_pair,'EURO', 'YEN', @b);
set new.ex_rate = @b;
update eu set ex_rate = 1/@b where currency=  'YEN';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;


elseif (new.currency = 'rupee') then
call sl_pair ('rupee', 'yen', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('jp', new.currency, @curr_pair,'yen','india',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('india',@pay, 'yen');
call up_e_rate(@curr_pair,'rupee', 'YEN', @b);
set new.ex_rate = @b;
update india set ex_rate = 1/@b where currency=  'YEN';
update currency_exch set latest_sp_m = @pay where currency_pair = @curr_pair;



end if;
end if;
end if;
end if ;

if (@t= 1 && new.currency = 'yen') then 
set new.curr_sply_m = new.curr_sply_m - @pay;
end if ;

end //

/* trigger for india*/

create trigger Tb_INDIA before update on india for each row
begin
set @t = 0;
if (new.trade_sr_df_m  < 0) then 
call check_value(new.currency, 'rupee', @a);
if (@a > 0 ) then
if (new.curr_sply_m > abs(new.trade_sr_df_m) ) then
if (new.currency ='POUND') then  
call up_supply('uk',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='USD') then 
call up_supply('us',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='yen') then 
call up_supply('jp',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

elseif (new.currency ='euro') then 
call up_supply('eu',new.trade_sr_df_m, new.currency);
set  new.curr_sply_m= new.curr_sply_m - abs(new.trade_sr_df_m);
set  new.trade_sr_df_m = new.trade_sr_df_m - new.trade_sr_df_m; 

end if;

else 

if (new.currency ='POUND') then  

call sl_pair ('POUND', 'rupee', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('india', new.currency, @curr_pair,'rupee','uk',@diff, @r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('uk',@pay, 'rupee');
call up_e_rate(@curr_pair,'POUND', 'rupee', @b);
set new.ex_rate = @b;
update uk set ex_rate = 1/@b where currency=  'rupee';
update currency_exch set latest_sp_m =@pay where currency_pair = @curr_pair;



elseif (new.currency = 'USD') then

call sl_pair ('rupee', 'usd', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('india', new.currency, @curr_pair,'rupee','us',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('us',@pay, 'rupee');
call up_e_rate(@curr_pair,'USD', 'rupee', @b);
set new.ex_rate = @b;
update us set ex_rate = 1/@b where currency=  'rupee';
update currency_exch set latest_sp_m =@pay where currency_pair = @curr_pair;

elseif ( new.currency = 'euro') then 
call sl_pair ('rupee', 'euro', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('india', new.currency, @curr_pair,'rupee','eu',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('eu',@pay, 'rupee');
call up_e_rate(@curr_pair,'euro', 'rupee', @b);
set new.ex_rate = @b;
update eu set ex_rate = 1/@b where currency=  'rupee';
update currency_exch set latest_sp_m =@pay where currency_pair = @curr_pair;

elseif (new.currency = 'yen') then
call sl_pair ('rupee', 'yen', @r);
set @curr_pair = @r;
set @diff = abs(new.trade_sr_df_m) - new.curr_sply_m;
call traders ('india', new.currency, @curr_pair,'rupee','jp',@diff,@r);
set @pay = @r * @diff;
set new.trade_sr_df_m = 0;
set new.curr_sply_m = 0;
set @t = 1;
call up_supply('jp',@pay, 'rupee');
call up_e_rate(@curr_pair,'YEN', 'rupee', @b);
set new.ex_rate = @b;
update jp set ex_rate = 1/@b where currency=  'rupee';
update currency_exch set latest_sp_m =@pay where currency_pair = @curr_pair;


end if;
end if;
end if;
end if ;

if (@t= 1 && new.currency = 'rupee') then 
set new.curr_sply_m = new.curr_sply_m - @pay;
end if ;

end //

/* this is the procedure  wher traders got choosen and activated and creating a responder who places the bid the exch_stram */
CREATE PROCEDURE `traders` (IN country varchar (20), IN bid_curr varchar(20), IN bid_pair varchar(20),IN ask_curr varchar(20), IN ask_country varchar(20),IN diff INT, OUT rate FLOAT)

BEGIN 

call active_trader (country, bid_curr, bid_pair, ask_curr,diff);

call active_respnder ( bid_curr, ask_curr, bid_pair);

call test_loop (bid_curr, ask_curr,country, ask_country, diff, @t);


set rate = @t;
 
end //

create procedure active_trader (IN country1 varchar (20), IN bid_curr varchar(20), IN bid_pair varchar(20),IN ask_curr varchar(20),IN diff INT)
begin
	call sl_trader1(country1, @t);
	set @trader = @t; 

	if ((bid_curr = 'USD' || bid_curr = 'YEN' ||bid_curr = 'rupee' || bid_curr = 'euro') && (ask_curr = 'POUND' || ask_curr = 'euro'|| ask_curr = 'USD' || ask_curr = 'rupee') ) then 
	call calculation1 ( bid_pair,diff, @b_rate);

	else call calculation (bid_pair,diff,@b_rate);
	end if ;
	

	INSERT INTO exch_stream( country,trader_id, bid_currency,bid_rate,ask_rate)
	VALUES(country1,@trader,bid_curr,@b_rate,'0');
end //

Create procedure calculation(IN bid_pair varchar(20), IN diff INT, OUT rate FLOAT)
begin
	set @s_price = (select latest_spotrate from currency_exch where currency_pair = bid_pair);

	set @bids = (select curr_num_bids from currency_exch where currency_pair = bid_pair);

	if (@bids = 0 ) then 
	set @b_rate = @s_price - (@s_price * 2)/100;
	else 
	set @s_rate = (select highest_bidp_m from currency_exch where currency_pair = bid_pair);
	set @s_rate = @s_rate /diff;
	set @b_rate = @s_rate + (@s_rate * 0.5)/100;
end if; 
set rate = @b_rate;
end //

Create procedure calculation1 (IN bid_pair varchar(20),IN diff INT, OUT rate FLOAT)
begin

	set @s_price = (select latest_spotrate from currency_exch where currency_pair = bid_pair);

	set @bids = (select curr_num_bids from currency_exch where currency_pair = bid_pair);
	set @s_price = 1 / @s_price;

	if (@bids = 0 || @bids = NULL) then 
	set @b_rate = (@s_price) - (@s_price * 2)/100;
	else 
	set @s_rate = (select highest_bidp_m from currency_exch where currency_pair = bid_pair);
	set @s_rate = @s_rate /diff;
	set @b_rate = (@s_rate) + (@s_rate * 0.5)/100;
	end if ;

set rate = @b_rate;


End //



CREATE PROCEDURE `sl_pair`( IN bidd_curr varchar(20), IN ask_curr varchar (20), OUT pair Varchar(20))
begin 

if ( (bidd_curr = 'USD' && ask_curr = 'POUND') || (bidd_curr = 'POUND' && ask_curr = 'USD')) then 
set @p = 'USD-POUND';
elseif ( (bidd_curr = 'euro' && ask_curr = 'POUND') || (bidd_curr = 'POUND' && ask_curr = 'euro')) then 
set @p = 'EURO-POUND';
elseif ( (bidd_curr = 'rupee' && ask_curr = 'euro') || (bidd_curr = 'euro' && ask_curr = 'rupee')) then 
set @p = 'RUPEE-EURO';
elseif ( (bidd_curr = 'rupee' && ask_curr = 'POUND') || (bidd_curr = 'POUND' && ask_curr = 'rupee')) then 
set @p = 'RUPEE-POUND';
elseif ( (bidd_curr = 'rupee' && ask_curr = 'USD') || (bidd_curr = 'USD' && ask_curr = 'rupee')) then 
set @p = 'RUPEE-USD';
elseif ( (bidd_curr = 'usd' && ask_curr = 'euro') || (bidd_curr = 'euro' && ask_curr = 'usd')) then 
set @p = 'USD-EURO';
elseif ( (bidd_curr = 'yen' && ask_curr = 'euro') || (bidd_curr = 'euro' && ask_curr = 'yen')) then 
set @p = 'YEN-EURO';
elseif ( (bidd_curr = 'yen' && ask_curr = 'POUND') || (bidd_curr = 'POUND' && ask_curr = 'yen')) then 
set @p = 'YEN-POUND';
elseif ( (bidd_curr = 'yen' && ask_curr = 'rupee') || (bidd_curr = 'rupee' && ask_curr = 'yen')) then 
set @p = 'YEN-RUPEE';
elseif ( (bidd_curr = 'yen' && ask_curr = 'usd') || (bidd_curr = 'usd' && ask_curr = 'yen')) then 
set @p = 'YEN-USD';


end if ;


set pair = @p;

end //




CREATE PROCEDURE `sl_trader1`( IN country varchar(20), OUT result Varchar(20))
begin 

if (country = 'us') then
set @p = 'US1';

elseif (country = 'uk') then

set @p = 'UK1';


elseif (country = 'india') then
set @p = 'IN1';

elseif (country = 'jp') then
set @p = 'JP1';

elseif (country = 'eu') then

set @p = 'EU1';
end if;

set result = @p;

end //




CREATE PROCEDURE `active_respnder` ( IN bid_curr varchar(20), IN ask_curr varchar(20), bid_pair varchar(20))
BEGIN

if ((bid_curr = 'USD' || bid_curr = 'YEN' ||bid_curr = 'rupee' || bid_curr = 'euro') && (ask_curr = 'POUND' || ask_curr = 'euro'|| ask_curr = 'USD' || ask_curr = 'rupee') ) then 

set @s_rate = (select latest_spotrate from currency_exch where currency_pair= bid_pair);
set @s_rate = 1/@s_rate;
set @a_rate = @s_rate + (@s_rate*2)/100;

else 

set @s_rate = (select latest_spotrate from currency_exch where currency_pair= bid_pair);
set @a_rate = @s_rate + (@s_rate*2)/100;
end if ;

if (bid_curr = 'POUND')then 
	
INSERT INTO exch_stream(trader_id, country,bid_currency,bid_rate,ask_currency,ask_rate)
	VALUES('uk1','uk','NULL','0',ask_curr,@a_rate);

elseif (bid_curr = 'USD')then 
	
INSERT INTO exch_stream(trader_id, country,bid_currency,bid_rate,ask_currency,ask_rate)
	VALUES('us1','us','NULL','0',ask_curr,@a_rate);

elseif (bid_curr = 'YEN')then 
	
INSERT INTO exch_stream(trader_id, country,bid_currency,bid_rate,ask_currency,ask_rate)
	VALUES('jp1','jp','NULL','0',ask_curr,@a_rate);

elseif (bid_curr = 'rupee')then 
	
INSERT INTO exch_stream(trader_id, country,bid_currency,bid_rate,ask_currency,ask_rate)
	VALUES('in1','india','NULL','0',ask_curr,@a_rate);
elseif (bid_curr = 'euro')then 
	
INSERT INTO exch_stream(trader_id, country,bid_currency,bid_rate,ask_currency,ask_rate)
	VALUES('eu1','eu','NULL','0',ask_curr,@a_rate);
end if ;

END//


create procedure `test_loop` (IN bid_curr varchar(20),IN ask_curr varchar(20), IN bid_country varchar(20), IN ask_country varchar(20),IN diff INT, OUT rate FLOAT)
BEGIN

set @bid_p = (select bid_rate from exch_stream where bid_currency = bid_curr and country = bid_country);

set @ask_p = (select ask_rate from exch_stream where ask_currency = ask_curr and country = ask_country);
call sl_pair(bid_curr, ask_curr, @p);

while (@bid_p != @ask_p ) do
set @n = (select count(*) from exch_stream where bid_currency = bid_curr );
set @q = (select count(*) from exch_stream where ask_currency = ask_curr );

if (@bid_p < @ask_p) then

if (@n >1 ) then 
update exch_stream set ask_rate = ask_rate + (ask_rate * 0.5)/100 where country = ask_country;
update exch_stream set bid_rate = bid_rate + (bid_rate)/100  where country= bid_country and bid_currency = bid_curr;
set @bid_p = (select bid_rate from exch_stream where bid_currency = bid_curr and country = bid_country);
set @ask_p = (select ask_rate from exch_stream where ask_currency = ask_curr and country = ask_country);
call up_stream_hl(@bid_p, @ask_p, @p, bid_curr, ask_curr, diff);	
update currency_exch set curr_num_bids =@n, curr_num_asks = @q, active_traders = curr_num_bids+@q where currency_pair = @p;


elseif (@n = 1) then
update exch_stream set bid_rate = bid_rate + (bid_rate)/100 where country= bid_country and bid_currency = bid_curr;
set @bid_p = (select bid_rate from exch_stream where bid_currency = bid_curr and country = bid_country);
set @ask_p = (select ask_rate from exch_stream where ask_currency = ask_curr and country = ask_country);
call up_stream_hl(@bid_p, @ask_p, @p, bid_curr, ask_curr, diff);	
update currency_exch set curr_num_bids =@n, curr_num_asks = @q, active_traders = curr_num_bids+@q where currency_pair =@p ;
end if ;

else
set @ask_p = @bid_p;

end if ;
end while;

call up_lsp (@bid_p,@p, bid_curr, ask_curr);

set rate = @bid_p;
if(bid_country = 'us') then 
set @trader = 'us1';
elseif (bid_country = 'uk')then 
set @trader = 'uk1';
elseif (bid_country = 'eu')then 
set @trader = 'eu1';
elseif (bid_country = 'jp')then 
set @trader = 'jp1';
elseif (bid_country = 'india')then 
set @trader = 'in1';
end if ;

update currency_exch set buyer = @trader, curr_num_bids = '0', curr_num_asks = '0',active_traders = '0' where currency_pair = @p;
delete from exch_stream where bid_currency = bid_curr and country = bid_country;
delete from exch_stream where ask_currency = ask_curr and country = ask_country;

END //


create procedure up_stream_hl (IN bid_p FLOAT,IN ask_p FLOAT, IN bid_pair varchar(20), IN bid_curr varchar(20), IN ask_curr varchar(20), IN diff INT)
begin 

if ((bid_curr = 'USD' || bid_curr = 'YEN' ||bid_curr = 'rupee' || bid_curr = 'euro') && (ask_curr = 'POUND' || ask_curr = 'euro'|| ask_curr = 'USD' || ask_curr = 'rupee') ) then 

update currency_exch set highest_bidp_m =(1/bid_p)*diff, lowest_askp_m = (1/ask_p)*diff where currency_pair = bid_pair;

else 
update currency_exch set highest_bidp_m = bid_p*diff, lowest_askp_m = ask_p * diff where currency_pair = bid_pair;

end if ;
end //


create procedure up_lsp (IN bid_p float, IN bid_pair varchar(20), IN bid_curr varchar(20), IN ask_curr varchar(20))
begin 
if ((bid_curr = 'USD' || bid_curr = 'YEN' ||bid_curr = 'rupee' || bid_curr = 'euro') && (ask_curr = 'POUND' || ask_curr = 'euro'|| ask_curr = 'USD' || ask_curr = 'rupee') ) then 

update currency_exch set latest_spotrate = 1/bid_p where currency_pair = bid_pair;

else 

update currency_exch set latest_spotrate = bid_p where currency_pair = bid_pair;
end if ;
end //



create procedure check_value (IN bid_curr varchar(20), IN ask_curr varchar(20), OUT amount INT)
begin 

if (bid_curr = 'POUND') then
set @v = (select trade_sr_df_m from uk where currency = ask_curr);
elseif (bid_curr = 'EURO') then 
set @v = (select trade_sr_df_m from eu where currency = ask_curr );
elseif (bid_curr = 'YEN') then 
set @v = (select trade_sr_df_m from jp where currency = ask_curr);
elseif (bid_curr = 'rupee') then 
set @v = (select trade_sr_df_m from india where currency = ask_curr);
elseif (bid_curr = 'USD') then 
set @v = (select trade_sr_df_m from us where currency = ask_curr);
end if ;

set amount = @v;

end //




create procedure up_e_rate (IN bid_pair varchar(20), IN bid_curr varchar(20), IN ask_curr varchar(20), out rate float) 
begin 
set @s_price = (select latest_spotrate from currency_exch where currency_pair = bid_pair);
if ((bid_curr = 'USD' || bid_curr = 'YEN' ||bid_curr = 'rupee' || bid_curr = 'euro') && (ask_curr = 'POUND' || ask_curr = 'euro'|| ask_curr = 'USD' || ask_curr = 'rupee') ) then 
set @r = 1/@s_price;
else
set @r = @s_price;
end if;

set rate = @r;


end //



CREATE PROCEDURE `up_supply` (IN country1 VARCHAR(20), IN trade INT , IN r_curr VARCHAR(40))
BEGIN 

if ( country1 = 'us' ) then 

update us
set curr_sply_m = curr_sply_m + abs(trade)
where currency = r_curr;

elseif country1 = 'uk' then

update uk
set curr_sply_m = curr_sply_m + abs(trade)
where currency = r_curr;

elseif country1 = 'india' then

update india
set curr_sply_m = curr_sply_m + abs(trade)
where currency = r_curr;

elseif country1 = 'jp' then

update jp
set curr_sply_m = curr_sply_m + abs(trade)
where currency = r_curr;

elseif country1 = 'eu' then

update eu
set curr_sply_m = curr_sply_m + abs(trade)
where currency = r_curr;

end if ;

 	
end //







