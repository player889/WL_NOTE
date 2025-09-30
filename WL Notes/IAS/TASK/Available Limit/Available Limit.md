
TODO

![[Pasted image 20230712170318.png]]


![[Pasted image 20230712165234.png]]
```SQL
    <select id="getAccountByActRfeNbrTxt" parameterType="String" resultMap="BaseResultMap">
        select acc.* from account acc
        where acc.act_rfe_nbr_txt = #{actRfeNbrTxt,jdbcType=CHAR}
    </select>
```

![[Pasted image 20230712164553.png|800]]
