<details>
<summary>Shell scripts</summary>

```bash
# setup.sh
#!#/bin/bash
# Run this script directly on the Zeek server

# ======================================================================
# CON FIGURATION VARIABLES - MODIFY
# Main execution
main(){
    echo "Setting up Zeek server environment... "
    echo""

    install_dependecies
    create_fake_servers
    create_documentation
    test_setup
    show_zeek_instructions

    echo -e "${GREEN}==========================================${NC}"
    echo -e "${GREEN}      Zeek Server Setup Complete!${NC}"
    echo -e "${GREEN}==========================================${NC}"
    echo ""
    echo "Next steps:"
    echo "1. Deploy Zeek rules manually"
    echo "2. Cheek Zeek logs: sudo tail -f ${ZEEK_LOGS_DIR}/notice.log"
    echo "3. Run fake server: python3 ${SCRIPTS_DIR}/c2_server.py"
    echo "4. Run detection script: sudo bash run_detections.sh"
    echo "" 

}

# Run the main function

```

</details>