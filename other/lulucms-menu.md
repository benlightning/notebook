# lulucms 中没有权限的菜单进行隐藏

```php
    // source/modules/menu/models/Menu.php
    public static function getChildren($category,$parentId,$status=null)
     {
        $roleItems = Lulu::getService('rbac')->getPermissionsByUser(Lulu::getIdentity()->username);
        $role = $reqFunc = [];
        foreach ($roleItems as $key => $val) {
            if (is_array($val[0]['value'])) {
                $reqFunc[$key] = array_map(function($v) {
                    return str_replace(array(':get', ':post'), '', $v);
                }, $val[0]['value']);
            }

            $role[] = $key; //explode('/',$key)[0];
        }
         $items = [];
         $menus = self::getMenusByCategory($category);
         foreach ($menus as $menu)
         {
            if (Lulu::getIdentity()->role != 'administrator' && $category == 'admin') {
                $pos = strpos($menu['url'], '&');
                $m_url = trim($menu['url'], '/');
                if ($pos) {
                    $m_url = substr($menu['url'], 0, $pos);
                    $m_url = trim($m_url, '/');
                }
                $m_url = explode('/', $m_url);
                if (isset($m_url[1])) {
                    $thekey = $m_url[0] . '/' . $m_url[1];
                    if (in_array($thekey, $role)) {
                        if (isset($m_url[2]) && !in_array($m_url[2], $reqFunc[$thekey])) {
                            continue;
                        }
                    } else {
                        continue;
                    }
                }
            }
            
            
             if($menu->parent_id === $parentId){
                 // 省略...
             }
             // ...
         }
```