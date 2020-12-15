ll base[65];

void add(ll x){
    for(int i=63;i>=0;i--){
        if(x&(1ll<<i)) {//第i+1位为1
            if(!base[i]) {
                base[i] = x;
                break;
            }
            x^=base[i];
        }
    }
}

void simplest(){
    for(int i=1;i<=63;i++){
        for(int j=0;j<i;j++){
            if(base[i]&(1ll<<j)){
                base[i]^=base[j];
            }
        }
    }
}

int get_size(){
    int i,base_number=0;
    for(i=0;i<=63;i++) {
        if(base[i]) base_number++;
    }
    return base_number;
}

ll find_kth(int k,int n){
    int i;
    ll ans=0;
    int base_number;
    base_number=get_size();
    if(k==1&&base_number<n) return 0;
    if(base_number<n) k--;
    if(k>pow(2,base_number)-1) return -1;
    simplest();
    for(i=0;i<=63;i++){
        if(base[i]){
            if(k%2==1) ans^=base[i];
            k/=2;
        }
    }
    return ans;
}

ll find_k(ll k){//异或和第k大
    ll i,ans=0;
    vector<int> ki;
    for(i=0;i<=63;i++){
        if(base[i]){
            ki.push_back(i);
        }
    }
    for(i=0;i<ki.size();i++){
        if((k>>ki[i])&1) ans+=pow(2,i);
    }
    return ans;
}

ll find_max(){
    ll ans=0;
    for(int i=63;i>=0;i--){
        if((ans^base[i])>ans) ans^=base[i];
    }
    return ans;
}
ll find_max(ll ans){
    for(int i=63;i>=0;i--){
        if((ans^base[i])>ans) ans^=base[i];
    }
    return ans;
}


ll quickPow(ll a,ll b,ll mod)          //  //(a^b)%mod
{
    ll res=1;
    while(b>0){
        if(b&1)
            res=(res%mod*a%mod)%mod;
        b>>=1;
        a=(a%mod*a%mod)%mod;
    }
    return res;
}
