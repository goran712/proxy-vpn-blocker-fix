# proxy-vpn-blocker-fix
The Proxy &amp; VPN Blocker plugin's sidebar checkbox was not appearing on custom post types (CPTs) due to an undefined meta value. 
This caused a JavaScript error in the file:
📂 /wp-content/plugins/proxy-vpn-blocker/assets/js/pvb-block-editor-script.js

Error Description
The script tries to read _pvb_checkbox_block_on_post from the post meta.

If the meta key is missing, the script throws an error, preventing the checkbox from being displayed.

Solution
The fix ensures that the meta object exists and assigns a default value (false) if the meta key is missing.

// Fixed Code

(function (plugins, editor, components, data, i18n, element) {
  var registerPlugin = plugins.registerPlugin;
  var PluginDocumentSettingPanel = wp.editPost.PluginDocumentSettingPanel;
  var CheckboxControl = components.CheckboxControl;
  var withSelect = data.withSelect;
  var withDispatch = data.withDispatch;
  var el = element.createElement;
  var __ = i18n.__;
  
  // Sidebar option registration
  registerPlugin('proxy-vpn-blocker-post-options', {
    render: withSelect(function (select) {
      var meta = select('core/editor').getEditedPostAttribute('meta') || {}; // Ensure meta exists
      return {
        metaValue: meta['_pvb_checkbox_block_on_post'] || false, // Default to false if undefined
      };
    })(withDispatch(function (dispatch, props) {
      return {
        setMetaValue: function (value) {
          dispatch('core/editor').editPost({ 
            meta: { '_pvb_checkbox_block_on_post': value } 
          });
        },
      };
    })(function (props) {
      var metaValue = props.metaValue;
      var setMetaValue = props.setMetaValue;
      
      // Convert undefined/null values to boolean
      var isChecked = !!metaValue;
      
      return el(PluginDocumentSettingPanel, {
            name: 'proxy-vpn-blocker-post-options',
            title: 'Proxy & VPN Blocker',
            icon: 'shield',
          },
          el(CheckboxControl, {
            label: __('Block on this Page/Post'),
            checked: isChecked,
            onChange: function (value) {
              setMetaValue(value);
            },
          })
      );
    })), 
  });
})(window.wp.plugins, window.wp.editor, window.wp.components, window.wp.data, window.wp.i18n, window.wp.element);

How to Apply the Fix
Replace the existing script in:
📂 /wp-content/plugins/proxy-vpn-blocker/assets/js/pvb-block-editor-script.js

Why This Works
✅ Prevents JavaScript errors by checking if the meta object exists before accessing keys.
✅ Ensures checkbox visibility on all post types, including CPTs.
✅ Prevents undefined values by setting a default (false) if the meta key is missing.

Contributing
If you have improvements or suggestions, feel free to submit a pull request. 
