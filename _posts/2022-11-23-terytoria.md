---
layout: "post"
title: "LOJ 3220 terytoria"
date: 2022-11-23
tags: hashing
---


### problem 

You have $n$ rectanges on an $X$ by $Y$ circular grid. This means that the top and bottom borders and left and right borders are connected. For each rectangle you can either take it or its complement (all the points not in the rectangle) and cover it. Overall $2^n$ ways to choose the rectangle or its determinant, what is the maximum area that is covered by all $n$ rectangle choices?

$1 \leq n \leq 5 \cdot 10^5, 0\leq x_1 \neq x_2, y_1 \neq y_2 < X, Y \leq 10^9$

### thoughts

the choices of rectangle's x axis's and y axis's are independant.

### solution

Since the $x$ and $y$ axis are independant, we will consider solving intervals on an axis instead of on a grid. We will also only solve the $x$ axis since the $y$ axis is the same.

For all points $i$ and $j$, if $i$ and $j$ are covered by the same set of input intervals, there is a way to pick intervals/complements such that $i$ and $j$ are both covered by all $n$ intervals in that choice. Consider all the intervals in that set, we will take them as is, and for everything else we will take the complement. This way $i$ and $j$ are both covered by all $n$ intervals.

Now we will shift out focus into counting for each set of input intervals, how many points are covered by exactly that set of input intervals. Since it is hard to maintain the actual set of intervals, this leads us to try and hash it. Also since we are hashing sets, this leads us to consider xor hashing. 

For each input interval $[l_i, r_i]$ lets pick a random number $h_i$. For each $x$, we will define $v_x$ as the xor of all the $h_i$ if $l_i \leq x \leq r_i$. We can then then count for each $c$ in $v$ the number of $x$ that have $v_x = c$. $v$ can be found using prefix sums, and counting can be done with a map.


Final code
```cpp

mt19937_64 rng(3366);

int n;
int X, Y;

ll hhh[MX], ggg[MX]; 

vector<pair<int, int>> dx, dy;

void solve(){
    n = in;
    X = in, Y = in;
    for(int i = 0; i<n; i++){
        hhh[i] = rng();

        int a = in, b = in, c = in, d = in;
        if(a > c) swap(a, c);
        if(b > d) swap(b, d);
        dx.pb({a, c}); 
        dy.pb({b, d});
    }
    array<ll, 2> ans = {0, 0};
    for(int t = 0; t<2; t++){
        vector<int> srt;
        for(auto [l, r] : dx){
            srt.pb(l); srt.pb(r);
        }        
        srt.pb(0);
        srt.pb(X);
        sort(all(srt)); srt.resize(unique(all(srt)) - srt.begin()); 
        vector<ll> pre(sz(srt), 0ll);
        for(int i = 0; i<n; i++){
            auto [l, r] = dx[i];
            l = lower_bound(all(srt), l) - srt.begin();
            r = lower_bound(all(srt), r) - srt.begin();
            pre[l] ^= hhh[i];
            pre[r] ^= hhh[i];
        }
        for(int i = 1; i<sz(pre); i++){
            pre[i] ^= pre[i-1];
        }
        ll last = 0;
        map<ll, ll> cnt;
        for(int i = 0; i<sz(srt); i++){
            if(i > 0){
                cnt[pre[i-1]] += srt[i] - last;
            }
            last = srt[i];
        }
        for(auto [v, c] : cnt){
            ans[t] = max(ans[t], c);
        }

        swap(dx, dy);
        swap(X, Y);
    }
    cout << 1ll*ans[0]*ans[1] << "\n"; 

}
```


### other notes

Be wary of the fact that $x_1 < x_2$ is not necessarily true in the input. Also discretization is needed.


Final complexity $O(n \log n)$.

code can be found [here.](https://loj.ac/s/1642722)